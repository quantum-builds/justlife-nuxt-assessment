<script setup lang="ts">
import { useRoute } from "vue-router";
import { useAsyncData } from "#app";

const route = useRoute();
const runtimeConfig = useRuntimeConfig();

const {
  data: articleData,
  error,
  pending: isLoading,
} = await useAsyncData(`article-${route.params.id}`, async () => {
  const apiKey = runtimeConfig.public.newApiKey;
  const response = await $fetch(`https://newsdata.io/api/1/latest`, {
    params: {
      apikey: apiKey,
      id: route.params.id,
    },
  });

  if (!response.results || !response.results.length) {
    throw new Error("Article not found");
  }

  return response.results[0];
});
</script>

<template>
  <div class="w-full min-h-screen bg-gray-900 text-white">
    <div class="container mx-auto px-4 py-10">
      <div v-if="isLoading" class="text-center text-gray-300 text-lg">
        Loading article details...
      </div>

      <div v-else-if="error" class="text-center text-red-500 text-lg">
        Failed to fetch article: {{ error.message }}
      </div>

      <div v-else class="bg-gray-800 rounded-lg shadow-lg p-6">
        <h1 class="text-4xl font-extrabold text-yellow-400 mb-4">
          {{ articleData.title }}
        </h1>

        <p class="text-gray-300 mb-6">
          Published: {{ new Date(articleData.pubDate).toLocaleString() }}
        </p>

        <img
          v-if="articleData.image_url"
          :src="articleData.image_url"
          alt="Article Image"
          class="w-full rounded-lg mb-6 object-cover"
        />

        <p class="text-gray-300 mb-6">
          {{ articleData.description || "No description available." }}
        </p>

        <a
          v-if="articleData.link"
          :href="articleData.link"
          target="_blank"
          rel="noopener noreferrer"
          class="text-blue-400 hover:underline"
        >
          Read More
        </a>
      </div>
    </div>
  </div>
</template>
