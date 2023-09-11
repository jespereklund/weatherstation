<script>
  import CountrySelector from "./CitySelector.svelte";
  import CityTile from "./CityTile.svelte";
  let selectedCity = {}
  let cities = []
  $: addCity(selectedCity)

  $: localStorage.setItem("cities", JSON.stringify(cities))

  if(localStorage.getItem("cities") != undefined) {
    cities = JSON.parse(localStorage.getItem("cities"))
  } 
  
  function addCity(city) {
    if (city != undefined) {
      if (city.name != null) {
        cities = [...cities, city]
      }
    }
  }

  function remove(index) {
    cities.splice(index, 1)
    cities = cities
  }
</script>
<main>
  <div style="position: relative;">
    <div style="height: 30px; position: absolute; z-index: 1;">
      <CountrySelector bind:selectedCity={selectedCity} />
    </div>
    <div style="position: absolute; top: 30px; z-index: 0; display: flex; flex-wrap: wrap; gap: 6px;">
      {#each cities as city, index}
        <div>
          <CityTile on:message={()=>{remove(index)}} data={city} />
      </div>
      {/each}
    </div>
  </div>
</main>