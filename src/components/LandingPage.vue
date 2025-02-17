<script setup>
import { ref, onMounted, watch, defineEmits, defineProps } from "vue";
import { createApi } from "unsplash-js";
import "../styles/landing.scss";

const props = defineProps({
  searchQuery: String,
});

const emit = defineEmits(["update:loading"]);

const photos = ref([]);
const selectedImage = ref(null);
const loading = ref(false);
const unsplash = createApi({
  accessKey: import.meta.env.VITE_UNSPLASH_ACCESS_KEY,
});

watch(
  () => props.searchQuery,
  async (newQuery) => {
    await fetchPhotos(newQuery);
  }
);

const fetchPhotos = async (query) => {
  loading.value = true;
  emit("update:loading", true);
  try {
    const { response } = await unsplash.search.getPhotos({
      query: query,
      orderBy: "latest",
      perPage: 8,
    });
    photos.value = response.results;
  } catch (error) {
    console.error("Error fetching photos:", error);
  } finally {
    loading.value = false;
    emit("update:loading", false);
  }
};

const openModal = (photo) => {
  selectedImage.value = photo;
};

const closeModal = () => {
  selectedImage.value = null;
};

onMounted(() => {
  fetchPhotos(props.searchQuery);
});
</script>

<template>
  <div class="photo-grid">
    <!-- Skeleton Loader -->
    <div v-if="loading" class="skeleton-loader">
      <div
        v-for="i in 8"
        :key="i"
        class="skeleton-item"
        :class="{ tall: i === 2 || i === 5 }"
      ></div>
    </div>

    <!-- Display Grid of Photos -->
    <div v-else class="grid">
      <div
        v-for="(photo, index) in photos"
        :key="photo.id"
        class="photo"
        :class="{ tall: index === 1 || index === 4 }"
        @click="openModal(photo)"
      >
        <img
          :src="photo.urls.small"
          :alt="photo.alt_description"
          class="fade-in"
        />
        <div class="overlay">
          <p class="author">{{ photo.user.name }}</p>
          <p class="location">
            {{ photo.user?.location || "" }}
          </p>
        </div>
      </div>
    </div>
  </div>

  <!-- Modal -->
  <div v-if="selectedImage" class="modal" @click="closeModal">
    <div class="modal-content" @click.stop>
      <img
        :src="selectedImage.urls.regular"
        :alt="selectedImage.alt_description"
        class="modal-image"
      />
      <div class="overlay2">
        <p class="author">{{ selectedImage.user.name }}</p>
        <p class="location">
          {{ selectedImage.user?.location || "" }}
        </p>
      </div>
    </div>
  </div>
</template>
