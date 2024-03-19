<template>
  <div>
    <!-- Banner -->
    <div class="flex flex-col flex-1 items-center">
      <div
        v-if="route.query.preview"
        class="text-white p-4 bg-weather-secondary w-full text-center"
      >
        <p>
          you are currently previewing this city, click the "+" icon to start
          tracing the city.
        </p>
      </div>
    </div>
    <!-- Weather -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ weatherData.name }}</h1>
      <p class="text-sm mb-12"></p>
      <p class="text-8xl mb-8">{{ Math.round(weatherData.main.temp) }}&deg C</p>
      <p class="mb-8">{{ new Date(weatherData.currentTime).toLocaleString('en-US', {
        weekday: "short",
        day: "2-digit",
        month: "long"
      }) }}</p>
      <p>
        Feels like
        {{ Math.round(weatherData.main.feels_like) }}
      </p>
      <p class="capitalize">{{ weatherData.weather[0].description }}</p>
      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
      />
    </div>
    <div class="flex items-center justify-center gap-2 text-white cursor-pointer duration-150 hover:text-red-500 text-center" @click="removeCity()">
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
  import axios from "axios";
  import { useRoute, useRouter } from "vue-router";

  const route = useRoute();
  const lat = route.query.lat;
  const lng = route.query.lng;
  const wApiToken = '73da27f34578d9c490ce44f57eaeddaa';
  const getWeatherData = async () => {
    try {
      const weatherData = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lng}&appid=${wApiToken}&units=metric`
      );
      // cal current data & time
      const localOffset = new Date().getTimezoneOffset() * 60000;
      const utc = weatherData.data.dt * 1000 + localOffset;
      weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone
      
      await new Promise((res) => setTimeout(res, 1000))  
      return weatherData.data;
    } catch (e) {
      console.log(e);
    }
  };
  const weatherData = await getWeatherData();

  const router = useRouter();
  const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem("savedCites"));
    const updateCities = cities.filter((city) => city.id != route.query.id);
    console.log(updateCities);
    localStorage.setItem('savedCites',JSON.stringify(updateCities));
    router.push({
      name:"home"
    });
  }
</script>
