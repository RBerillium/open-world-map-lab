<script>
    import { onMount } from "svelte";
    import "ol/ol.css";
    import Map from "ol//Map";
    import View from "ol/View";
    import TileLayer from "ol/layer/Tile";
    import OSM from "ol/source/OSM";
    import VectorLayer from "ol/layer/Vector";
    import VectorSource from "ol/source/Vector";
    import Feature from "ol/Feature";
    import Point from "ol/geom/Point";
    import { Circle as CircleStyle, Fill, Stroke, Style } from "ol/style";
    import { fromLonLat } from "ol/proj";
  
    let mapContainer;
  
    // ну вот и апишечка
    async function fetchCities() {
      const url = "https://public.opendatasoft.com/api/records/1.0/search/?dataset=geonames-all-cities-with-a-population-1000&q=&rows=1000&sort=population";
      const response = await fetch(url);
      const data = await response.json();
  
      return data.records.map(record => ({
        name: record.fields.name,
        coords: [record.fields.coordinates[1], record.fields.coordinates[0]],
        population: record.fields.population
      }));
    }
  
    onMount(async () => {
      const map = new Map({
        target: mapContainer,
        layers: [
          new TileLayer({
            source: new OSM(), 
          }),
        ],
        view: new View({
          center: fromLonLat([10, 20]), 
          zoom: 2,
        }),
      });
  
      // Загружаем города из API
      const cities = await fetchCities();
  
      const cityLayer = new VectorLayer({
        source: new VectorSource({
          features: cities.map(city => {
            const feature = new Feature({
              geometry: new Point(fromLonLat(city.coords)),
              name: city.name
            });
  
            const radius = Math.sqrt(city.population) / 1500; // типа комментарий нейросети
  
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
      overflow: hidden;
    }
  
    #map {
      width: 100%;
      height: 100%;
    }
  </style>
  
  <div class="map-container">
    <div id="map" bind:this={mapContainer}></div>
  </div>
  