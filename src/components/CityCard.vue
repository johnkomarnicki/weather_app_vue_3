<template>
  <div
    class="flex py-4 px-2 bg-primary-green rounded-md shadow-md"
  >
    <div class="flex flex-col flex-1">
      <h1 class="text-3xl">{{ city.city }}</h1>
      <h2>{{ city.state }}</h2>
    </div>
    <div class="flex flex-col gap-2">
      <!-- Current Weather -->
      <p v-if="currentWeather" class="text-3xl self-end">
        {{ Math.round(currentWeather.main.temp) }}&deg;
      </p>
      <!-- Current Weather Skelton -->
      <p
        v-else
        class="animate-pulse bg-gradient-to-r from-gray-50 w-10 self-end"
      >
        &nbsp;
      </p>
      <div v-if="currentWeather" class="flex gap-2">
        <span class="text-xs">
          H:
          {{ Math.round(currentWeather.main.temp_max) }}&deg;
        </span>
        <span class="text-xs">
          L:
          {{ Math.round(currentWeather.main.temp_min) }}&deg;
        </span>
      </div>
      <div v-else class="flex gap-2">
        <span
          class="animate-pulse bg-gradient-to-r from-gray-50 w-10"
        >
          &nbsp;
        </span>
        <span
          class="animate-pulse bg-gradient-to-r from-gray-50 w-10"
        >
          &nbsp;
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";

const props = defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});

const currentWeather = ref(null);
const openWeatherAPIkey = "d8192170b76649bac31b723e270b821e";

const flickerDelay = () => {
  return new Promise((res) => setTimeout(() => res(), 2000));
};

const getWeather = async () => {
  await flickerDelay();
  try {
    const result = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${props.city.coords.lat}&lon=${props.city.coords.lng}&appid=${openWeatherAPIkey}&units=imperial`
    );
    currentWeather.value = result.data;
  } catch (err) {
    console.log(err);
  }
};

getWeather();
</script>
