<script setup>
import { onMounted, ref, inject } from 'vue'

import CardList from '../components/CardList.vue'

const favorites = ref([])

const { onClickAddPlus } = inject('cart')

const fatchFavorites = () => {
  fetch(`https://f6f031af57a38201.mokky.dev/favorites`)
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка HTTP: ${response.status}`)
      }

      return response.json()
    })
    .then((data) => {
      favorites.value = data.map((obj) => obj.item)
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

onMounted(async () => {
  await fatchFavorites()
})
</script>

<template>
  <section class="py-10">
    <div class="mx-auto px-20 max-w-7xl w-full">
      <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>

      <CardList :items="favorites" @addToCart="onClickAddPlus" isFavorites />
    </div>
  </section>
</template>
