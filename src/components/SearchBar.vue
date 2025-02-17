<script setup>
import { ref, defineEmits, defineProps } from "vue";
import "../styles/search-bar.scss";
const props = defineProps({
  loading: Boolean,
});

const searchQuery = ref("");
const emit = defineEmits(["update:searchQuery"]);

const searchPhotos = () => {
  emit("update:searchQuery", searchQuery.value);
};
</script>

<template>
  <div class="search-bar">
    <input
      type="text"
      v-model="searchQuery"
      placeholder="Search for photos..."
      @input="searchPhotos"
    />
    <p v-if="searchQuery && loading" class="searching-message">
      Searching for <span class="search-query">"{{ searchQuery }}"</span>
    </p>
    <p v-if="searchQuery && !loading" class="searching-message">
      Search Results for <span class="search-query">"{{ searchQuery }}"</span>
    </p>
  </div>
</template>
