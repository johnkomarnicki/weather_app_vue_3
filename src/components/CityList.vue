<template>
  <div v-for="city in savedCities" :key="city.id">
    <!-- Card -->
    <CityCard
      :city="city"
      @click="goToCityView(city)"
      @remove-city="removeCity"
    />
  </div>

  <!-- No Results -->
  <h2 v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in
    the field above.
  </h2>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";

const savedCities = ref([]);

// Get Cities
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    );
  }

  const requests = [];
  const openWeatherAPIkey = "d8192170b76649bac31b723e270b821e";
  savedCities.value.forEach((city) => {
    requests.push(
      axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lng}&appid=${openWeatherAPIkey}&units=imperial`
      )
    );
  });

  const weatherData = await Promise.allSettled(requests);

  // Flicker Delay
  await new Promise((res) => setTimeout(res, 1000));

  weatherData.forEach((value, index) => {
    savedCities.value[index].weather = value.value.data;
  });
};

// Remove Cities
const removeCity = (id) => {
  savedCities.value = savedCities.value.filter(
    (city) => city.id !== id
  );
  localStorage.setItem(
    "savedCities",
    JSON.stringify(savedCities.value)
  );
};

// Go to City View
const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: { lat: city.coords.lat, lng: city.coords.lng },
  });
};

// Invoke Function
await getCities();
</script>

<style lang="scss" scoped></style>
