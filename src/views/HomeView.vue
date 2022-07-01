<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";
import BaseInput from "../components/BaseInput.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
import CityList from "../components/CityList.vue";

const router = useRouter();
const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};

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
  <main class=" relative">
    <div class="container text-white">
      <!-- Search -->
      <div class="pt-4 mb-8 relative">
        <BaseInput
          v-model="searchQuery"
          placeholder="Search for a city or state"
          class=" 
          w-full 
          bg-transparent
          border-b-[1px]
          focus:border-day-secondary
          focus:outline-none 
          focus:shadow-[0px_1px_0_0_#004E71]
          relative"
          @input="getSearchResults"
        />

        <!-- Search Results -->
        <ul
          class="absolute bg-day-secondary text-white w-full shadow-md py-2  px-1 top-[66px]"
          v-if="searchQuery && mapBoxSearchResults"
        >
          <li
            v-for="searchResult in mapBoxSearchResults"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </ul>
      </div>

      <!-- Cities List -->
      <div class="flex flex-col gap-4">
        <Suspense>
          <CityList />
          <!-- Card Skelton -->
          <template #fallback>
            <CityCardSkeleton />
          </template>
        </Suspense>
      </div>
    </div>
  </main>
</template>
