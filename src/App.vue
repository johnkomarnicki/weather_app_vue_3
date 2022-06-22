<script setup>
import { RouterView } from "vue-router";
import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<template>
  <div class="min-h-screen bg-dark-green">
    <SiteNavigation />
    <RouterView />
  </div>
</template>
