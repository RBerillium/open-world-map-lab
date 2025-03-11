<script>
  import Map from "./lib/Map.svelte"; // Подключаем карту
</script>

<main>
  <h1>Карта населения городов</h1>
  <Map />
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
</style>
