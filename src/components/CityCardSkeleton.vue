<template>
  <div class="flex p-6 bg-weather-secondary rounded-md shadow-md mb-4">
    <div class="flex flex-col flex-1 gap-2">
      <AnimatedPlaceholder class="max-w-[50%]"/>
      <AnimatedPlaceholder class="max-w-[40%]"/>
    </div>
    <div class="flex flex-col  item-end flex-1 gap-2">
      <AnimatedPlaceholder class="max-w-[50px] w-full"/>
      <AnimatedPlaceholder class="max-w-[75px] w-full"/>
    </div>
  </div>
</template>

<script setup>
  import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";
</script>
