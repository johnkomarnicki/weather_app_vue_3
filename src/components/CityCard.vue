<template>
  <div
    class="flex py-6 px-3 bg-day-secondary rounded-md shadow-md  cursor-pointer"
  >
    <div class="flex flex-col flex-1">
      <h1 class="text-3xl">{{ city.city }}</h1>
      <h2>{{ city.state }}</h2>
    </div>

    <div class="flex flex-col gap-2">
      <!-- Current Weather -->
      <p class="text-3xl self-end">
        {{ Math.round(city.weather.main.temp) }}&deg;
      </p>
      <div class="flex gap-2">
        <span class="text-xs">
          H:
          {{ Math.round(city.weather.main.temp_max) }}&deg;
        </span>
        <span class="text-xs">
          L:
          {{ Math.round(city.weather.main.temp_min) }}&deg;
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
</script>
