<script setup>
import { ref } from "vue";
import { uid } from "uid";
import axios from "axios";
import BaseInput from "../components/BaseInput.vue";
import CityCard from "../components/CityCard.vue";
import CitySkeleton from "../components/CitySkeleton.vue";

// Add City
const savedCities = ref([]);

const addCity = async (searchResult) => {
  const locationName = searchResult.place_name.split(",");
  const locationObj = {
    id: uid(),
    city: locationName[0],
    state: locationName[1].replaceAll(/\s/g, ""),
    coords: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem(
    "savedCities",
    JSON.stringify(savedCities.value)
  );

  // reset search
  searchQuery.value = "";
  mapBoxSearchResults.value = null;
};

const removeCity = (id) => {
  savedCities.value = savedCities.value.filter(
    (city) => city.id !== id
  );
  localStorage.setItem(
    "savedCities",
    JSON.stringify(savedCities.value)
  );
};

// Check Local Storage For Cities
const getCities = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    );
  }
};
getCities();

// Geolocation
const mapBoxAPIkey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const queryTimeout = ref(null);
const mapBoxSearchResults = ref(null);
const searchQuery = ref("");
const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      const results = await axios.get(
        `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapBoxAPIkey}&types=place`
      );
      mapBoxSearchResults.value = results.data.features;
      return;
    }
    mapBoxSearchResults.value = null;
  }, 300);
};
</script>

<template>
  <main>
    <div class="container text-white">
      <!-- Search -->
      <div class="pt-4 mb-4">
        <BaseInput
          v-model="searchQuery"
          placeholder="Search for a city or state"
          class=" 
          w-full 
          bg-transparent
          border-b-[1px]
          focus:border-bright-green
          focus:outline-none 
          focus:shadow-[0px_1px_0_0_#15FA8E]"
          @input="getSearchResults"
        />
      </div>

      <!-- Search Results -->
      <ul>
        <li
          v-for="searchResult in mapBoxSearchResults"
          class="py-2 cursor-pointer"
          @click="addCity(searchResult)"
        >
          {{ searchResult.place_name }}
        </li>
      </ul>

      <!-- Local Storage Results -->
      <div class="flex flex-col gap-4">
        <Suspense>
          <!-- Card -->
          <template #default>
            <div v-for="(city, index) in savedCities">
              <CityCard
                :city="city"
                :key="city.id"
                @remove-city="removeCity"
              />
            </div>
          </template>

          <!-- Card Skelton -->
          <template #fallback>
            <CitySkeleton
              v-for="i in savedCities.length"
              :key="i"
            />
          </template>
        </Suspense>
      </div>

      <!-- No Results -->
      <h2
        v-if="!mapBoxSearchResults && savedCities.length === 0"
      >
        No locations added. To start tracking a location, search
        in the field above.
      </h2>
    </div>
  </main>
</template>
