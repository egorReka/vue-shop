<script setup>
import CardSneakers from './CardSneakers.vue'

defineProps({
  items: Array,
  isFavorites: Boolean,
})

// emmit используем при прокидывании function
const emmit = defineEmits(['onClickButtonFavorite', 'addToCart'])
</script>

<template>
  <ul class="grid grid-cols-[repeat(auto-fill,_minmax(180px,_1fr))] gap-4">
    <li v-for="item in items" :key="item.id">
      <CardSneakers
        :image-url="item.imageUrl"
        :id="item.id"
        :title="item.title"
        :price="item.price"
        :is-processing="item.isProcessing"
        :is-favorite="item.isFavorite"
        :onClickAdd="() => emmit('addToCart', item)"
        :onClickButtonFavorite="isFavorites ? null : () => emmit('onClickButtonFavorite', item)"
        :isAdded="item.isAdded"
      />
    </li>
  </ul>
</template>
