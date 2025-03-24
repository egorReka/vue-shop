<script setup>
import { inject } from 'vue'

import CartItem from './CartItem.vue'
const { cart, removeFromCart } = inject('cart')
</script>

<template>
  <ul class="grid gap-6">
    <li v-for="item in cart" :key="item.id">
      <CartItem
        :title="item.title"
        :price="item.price"
        :image-url="item.imageUrl"
        @on-click-remove="() => removeFromCart(item)"
      />
    </li>
  </ul>
</template>
