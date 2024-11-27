<template>
  <div class="w-full h-screen flex justify-center items-center">
    <div
      class="flex flex-col gap-4 items-center p-6 bg-gray-700 rounded-lg shadow-lg"
    >
      <h1 class="font-black text-5xl text-yellow-400 drop-shadow-lg">
        News Viewer
      </h1>
      <NuxtLink
        to="/news"
        class="px-4 py-2 bg-yellow-400 text-gray-900 font-semibold rounded-lg shadow-md hover:bg-yellow-500 hover:text-gray-800 transform hover:scale-105 transition-transform duration-300"
      >
        Click here to see the news
      </NuxtLink>
    </div>
  </div>
</template>
