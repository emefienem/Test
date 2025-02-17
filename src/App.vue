<script setup>
import { ref } from "vue";
import SearchBar from "./components/SearchBar.vue";
import LandingPage from "./components/LandingPage.vue";

const searchQuery = ref("African");
const loading = ref(false);

const handleSearchQueryUpdate = (newQuery) => {
  searchQuery.value = newQuery;
  loading.value = true;
};
</script>

<template>
  <div>
    <SearchBar
      :loading="loading"
      @update:searchQuery="handleSearchQueryUpdate"
    />
    <LandingPage
      :searchQuery="searchQuery"
      @update:loading="(newLoading) => (loading = newLoading)"
    />
  </div>
</template>
