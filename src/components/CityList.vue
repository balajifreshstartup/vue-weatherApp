<template>
  <div>
    <div v-for="city in savedCites" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)"/>
    </div>
    <p v-if="savedCites.length === 0">
      No location added. To start tracking a location, search in the field above.
    </p>
  </div>
</template>

<script setup>
  import CityCard from "./CityCard.vue";
  import axios from "axios";
  import { ref } from "vue";
  import {useRouter} from "vue-router";

  const savedCites = ref([]);
  const wApiToken = '73da27f34578d9c490ce44f57eaeddaa';
  const getCities = async () => {
    if (localStorage.getItem("savedCites")) {
      savedCites.value = JSON.parse(localStorage.getItem("savedCites"));
      const requests = [];
      savedCites.value.forEach((city) => {
        requests.push(
          axios.get(
            `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${wApiToken}&units=metric`
          )
        );
      });

      const weatherData = await Promise.all(requests);
      // Flicker Delay
      await new Promise((res) => setTimeout(res, 1000));
      weatherData.forEach((value, index) => {
        savedCites.value[index].weather = value.data;
      });
    }
  };
  await getCities();
  const router = useRouter();
  const goToCityView = (city) => {
    router.push({
      name: "cityView",
      params: {state:city.state,city:city.city},
      query:{id:city.id, lat: city.coords.lat,lng: city.coords.lng}
    })
  }
</script>
