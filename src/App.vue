<script setup>
import { ref, watch, provide, computed } from 'vue'

import AppHeader from './components/AppHeader.vue'

import Drawer from './components/Drawer.vue'

const cart = ref([])
const cartOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))

const addToFavorite = (item) => {
  if (item.isProcessing) {
    return
  }

  item.isProcessing = true
  item.isFavorite = true

  const obj = {
    parentId: item.id,
    item,
  }

  fetch(`https://f6f031af57a38201.mokky.dev/favorites`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json;charset=utf-8',
    },
    body: JSON.stringify(obj),
  })
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка HTTP: ${response.status}`)
      }

      return response.json()
    })
    .then((result) => {
      item.favoriteId = result.id
    })
    .catch((error) => {
      console.error('Ошибка при отправке данных:', error)
    })
    .finally(() => {
      item.isProcessing = false
    })
}

const removeFromFavorite = (item) => {
  if (item.isProcessing) {
    return
  }

  item.isProcessing = true
  item.isFavorite = false

  fetch(`https://f6f031af57a38201.mokky.dev/favorites/${item.favoriteId}`, {
    method: 'DELETE',
  })
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка HTTP: ${response.status}`)
      }

      return response.text()
    })
    .then(() => {
      item.favoriteId = null
    })
    .catch((error) => {
      console.error('Ошибка при удалении данных:', error)
    })
    .finally(() => {
      item.isProcessing = false
    })
}

const onClickButtonFavorite = (item) => {
  if (!item.favoriteId) {
    addToFavorite(item)
  } else {
    removeFromFavorite(item)
  }
}

provide('favorite', { onClickButtonFavorite })

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const onClickOpenCart = () => {
  cartOpen.value = true
}

const onClickCloseCart = () => {
  cartOpen.value = false
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

provide('cart', {
  cart,
  addToCart,
  removeFromCart,
  onClickAddPlus,
})

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true },
)
</script>

<template>
  <div class="bg-[#E7F6FF] p-10">
    <div
      class="grid grid-rows-[auto_1fr] bg-white min-h-screen rounded-3xl max-w-7xl mx-auto w-full"
    >
      <AppHeader @on-click-open-cart="onClickOpenCart" :total-price="totalPrice" />

      <main>
        <router-view></router-view>
      </main>

      <Drawer v-if="cartOpen" :total-price="totalPrice" @on-click-close-cart="onClickCloseCart" />
    </div>
  </div>
</template>

<style scoped></style>
