<script setup>
defineProps({
  title: String,
  description: String,
  imageUrl: String,
})
</script>

<template>
  <div class="self-center py-10">
    <img
      class="block mx-auto mb-10"
      :src="imageUrl"
      width="120"
      height="120"
      alt="Информационная картинка."
    />
    <h2 class="mb-4 text-2xl font-medium text-center">{{ title }}</h2>
    <p class="text-gray-400 text-center">{{ description }}</p>
  </div>
</template>
