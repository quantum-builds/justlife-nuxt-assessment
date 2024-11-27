<script setup lang="ts">
import type { NewsApiResponse } from "~/types/new";

const runtimeConfig = useRuntimeConfig();

const nextPage = ref<string | undefined>(undefined);
const pages = ref<string[]>([]);

const API_URL = computed(() => {
  const baseURL = `https://newsdata.io/api/1/latest?size=10&apikey=${runtimeConfig.public.newApiKey}`;
  return pages.value.length ? `${baseURL}&page=${pages.value.at(-1)}` : baseURL;
});

const fetchData = async () => {
  const response = await $fetch<NewsApiResponse>(API_URL.value);

  if (response.nextPage) {
    pages.value.push(response.nextPage);
    nextPage.value = response.nextPage;
  } else {
    nextPage.value = undefined;
  }

  return response;
};

const {
  data,
  error,
  pending: isLoading,
  refresh,
} = await useAsyncData<NewsApiResponse>("newsData", fetchData);

const newData = computed(() => {
  return (
    data.value?.results.map((article) => ({
      ...article,
      trimmedDescription: article.description
        ? article.description.length > 100
          ? article.description.substring(0, 100) + "..."
          : article.description
        : "No description available.",
    })) || []
  );
});

const handlePageChange = async (direction: "next" | "prev") => {
  if (direction === "next" && nextPage.value) {
    await refresh();
  } else if (direction === "prev" && pages.value.length > 1) {
    nextPage.value = pages.value.at(-2);
    pages.value.pop();
    await refresh();
  }
};
</script>

<template>
  <div class="w-full min-h-screen bg-gray-900 text-white">
    <div class="container mx-auto px-4 py-10">
      <div class="text-center mb-12">
        <h1 class="text-4xl font-extrabold text-yellow-400 drop-shadow-lg">
          News Page
        </h1>
        <p class="text-gray-300 mt-4 text-lg">
          Stay updated with the latest news from around the world.
        </p>
      </div>

      <div v-if="isLoading" class="text-center text-gray-300 text-lg">
        Loading...
      </div>

      <div v-else-if="error" class="text-center text-red-500 text-lg">
        Failed to fetch news: {{ error.message }}
      </div>

      <div
        v-else-if="!newData.length"
        class="text-center text-gray-300 text-lg"
      >
        No news articles available.
      </div>

      <div v-else>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8">
          <NewsCard
            v-for="(article, index) in newData"
            :key="article.article_id || index"
            :article="article"
          />
        </div>

        <div class="flex justify-between items-center mt-12">
          <button
            @click="handlePageChange('prev')"
            :disabled="pages.length <= 1"
            class="bg-gray-700 hover:bg-gray-600 text-white px-6 py-3 rounded disabled:opacity-50 disabled:cursor-not-allowed"
          >
            Previous
          </button>

          <span class="text-gray-300 font-semibold text-lg">
            Page {{ pages.length }}
          </span>

          <button
            @click="handlePageChange('next')"
            :disabled="!nextPage"
            class="bg-gray-700 hover:bg-gray-600 text-white px-6 py-3 rounded disabled:opacity-50 disabled:cursor-not-allowed"
          >
            Next
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
