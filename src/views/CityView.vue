<template>
  <div>
    <Suspense>
      <AsynCityWeather />
      <template #fallback><CityViewSkeletonVue /></template>
    </Suspense>
  </div>
</template>

<script setup>
  import { defineAsyncComponent } from "vue";
  import CityViewSkeletonVue from "@/components/CityViewSkeleton.vue";
  const AsynCityWeather = defineAsyncComponent(() =>
    import("../components/AsynCityWeather.vue")
  );
</script>
