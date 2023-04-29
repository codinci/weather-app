<script setup>
import { ref } from 'vue';
import axios from 'axios';

const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null)
const getSearchResults = () => {
  const MAPBOX_API_KEY = import.meta.env.VITE_MAPBOX_API_KEY;

  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async() => {
    if (searchQuery.value !== '') {
      const result = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}
        .json?access_token=${MAPBOX_API_KEY}&types=place`
      );
      mapboxSearchResults.value = result.data.features;
      return;
    }
    mapboxSearchResults.value = null
  }, 300)

}
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
      type="text"
      v-model="searchQuery"
      @input="getSearchResults"
      placeholder="search for a city or state"
      class="py-2 pt-1 w-full bg-transparent border-b focus:border-weather-secondary
      focus:outline-none focus: shadow-[0px_1px_0_0_#004E71]"
      />
      <ul class="absoute bg-weather-secondary text-white
       w-full shadow-md px-1 py-2 top-[66px]"
       v-if="mapboxSearchResults">
        <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id"
        class="py-2 cursor-pointer">
      {{searchResult.place_name}}
      </li>
      </ul>
    </div>
  </main>
</template>
