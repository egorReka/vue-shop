<script setup>
import CartItem from './CartItem.vue'

defineProps({
  items: Array,
})
</script>

<template>
  <ul class="grid gap-6">
    <li>
      <CartItem />
    </li>
    <li>
      <CartItem />
    </li>
  </ul>
</template>
