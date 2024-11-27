<template>
  <div class="bg-gray-800 rounded-lg shadow-lg overflow-hidden">
    <img
      v-if="article.image_url"
      :src="article.image_url"
      alt="Article Image"
      class="w-full h-48 object-cover"
    />
    <div class="p-6">
      <h2 class="text-xl font-bold mb-4 text-yellow-300">
        {{ article.title }}
      </h2>
      <p class="text-gray-300 mb-6">
        {{ article.trimmedDescription }}
      </p>
      <NuxtLink
        :to="{ name: 'news-id', params: { id: article.article_id } }"
        class="text-blue-400 hover:underline"
      >
        Read More
      </NuxtLink>
    </div>
  </div>
</template>

<script setup lang="ts">
import type { Article } from "~/types/new";

defineProps<{
  article: Article & { trimmedDescription: string };
}>();
</script>
