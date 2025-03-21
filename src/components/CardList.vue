<script setup>
import CardSneakers from './CardSneakers.vue'

defineProps({
  items: Array,
})

const onClickAdd = () => {
  alert('Добавить в корзину')
}
</script>

<template>
  <ul class="grid grid-cols-[repeat(auto-fit,_minmax(180px,_1fr))] gap-4">
    <li v-for="item in items" :key="item.id">
      <CardSneakers
        :image-url="item.imageUrl"
        :title="item.title"
        :price="item.price"
        :is-added="false"
        :is-favorite="false"
        :onClickAdd="onClickAdd"
      />
    </li>
  </ul>
</template>
