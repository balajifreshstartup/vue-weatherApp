<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResult"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        v-if="mapboxSearchResult"
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
      >
      <p v-if="serverError"> Sorry, something went wrong, please try again.</p>
      <p v-if="!serverError && mapboxSearchResult.length === 0">
        No result match for your query, try a different term.
      </p>
        <li
          v-for="searchResult in mapboxSearchResult"
          :key="searchResult.id"
          @click="previewCity(searchResult)"
          class="py-2 cursor-pointer"
        >
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CardList/>
        <template #fallback>
          <CityCardSkeleton/>
        </template>
      </Suspense>
    </div>
  </main>
</template>
<script setup>
    import {ref} from 'vue'
    import axios from 'axios'
    import {useRouter} from 'vue-router'
    import CardList from '../components/CityList.vue'
    import CityCardSkeleton from '../components/CityCardSkeleton.vue'

    const router = useRouter();
    const mapboxApiKey = 'pk.eyJ1IjoiYmFsYWppMjIiLCJhIjoiY2x0YzkxZ2c4MXptaDJqbzFqamQ1a2ZoYSJ9.N-vLWTt4B2pblL18FZ4cSQ';
    const searchQuery = ref("")
    const queryTimeOut = ref(null)
    const mapboxSearchResult = ref(null)
    const serverError = ref(null)
  const getSearchResult = (() => {
    queryTimeOut.value = setTimeout(async () => {
      clearTimeout(queryTimeOut.value);
      if(searchQuery.value !== ''){
        try{
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxApiKey}&type=place`)
        mapboxSearchResult.value = result.data.features
        }catch{
          serverError.value = true;
        }
        return
      }
      mapboxSearchResult.value = null;
    },300)
  })
  const previewCity = (searchResult) => {
    const [city, state] = searchResult.place_name.split(',');
    router.push({
      name: 'cityView',
      params: {state:state.replaceAll(" ",""), city:city},
      query: {
        lat: searchResult.geometry.coordinates[1],
        lng: searchResult.geometry.coordinates[0],
        preview: true
      }
    })
  }
</script>
