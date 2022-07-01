<script setup>
import { RouterView, useRoute } from "vue-router";
import SiteNavigation from "./components/SiteNavigation.vue";

const route = useRoute();
</script>

<template>
  <div
    class="flex flex-col min-h-screen font-Roboto bg-day-primary"
  >
    <SiteNavigation />
    <RouterView class="flex-1" v-slot="{ Component }">
      <Transition name="page">
        <component :is="Component" />
      </Transition>
    </RouterView>
  </div>
</template>

<style>
.page-enter-active {
  transition: 600ms ease all;
}

.page-enter-from {
  opacity: 0;
}
</style>
