<script>
  import { onMount } from "svelte";
  import "ol/ol.css";
  import Map from "ol/Map";
  import View from "ol/View";
  import TileLayer from "ol/layer/Tile";
  import OSM from "ol/source/OSM";
  import VectorLayer from "ol/layer/Vector";
  import VectorSource from "ol/source/Vector";
  import Feature from "ol/Feature";
  import Point from "ol/geom/Point";
  import { Circle as CircleStyle, Fill, Stroke, Style } from "ol/style";
  import { fromLonLat } from "ol/proj";
  import { cities } from "./cities.js";


  let mapContainer;

  onMount(() => {
    // Создаём карту
    const map = new Map({
      target: mapContainer,
      layers: [
        new TileLayer({
          source: new OSM(),
        }),
      ],
      view: new View({
        center: fromLonLat([0, 30]), // Центрируем карту (примерное расположение)
        zoom: 2,
      }),
    });

    // Создаём слой с городами
    const cityLayer = new VectorLayer({
      source: new VectorSource({
        features: cities.map((city) => {
          const feature = new Feature({
            geometry: new Point(fromLonLat(city.coords)),
            name: city.name,
          });

          // Размер окружности зависит от населения
          const radius = Math.sqrt(city.population) / 1000;

          feature.setStyle(
            new Style({
              image: new CircleStyle({
                radius: radius,
                fill: new Fill({ color: "rgba(255, 0, 0, 0.6)" }),
                stroke: new Stroke({ color: "#ff0000", width: 1 }),
              }),
            })
          );

          return feature;
        }),
      }),
    });

    map.addLayer(cityLayer);
  });
</script>

<style>
  .map-container {
    width: 100vw;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    overflow: hidden; /* Убирает скроллы */
  }

  #map {
    width: 80%;
    height: 90%;
  }
</style>

<div class="map-container">
  <div id="map" bind:this={mapContainer}></div>
</div>
