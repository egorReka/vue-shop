<script setup>
import { ref, watch, provide, computed } from 'vue'

import AppHeader from './components/AppHeader.vue'

import Drawer from './components/Drawer.vue'

const cart = ref([])
const cartOpen = ref(false)
const isCreatingOrder = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const cartIsEmpty = computed(() => cart.value.length === 0)
const disabledButton = computed(() => isCreatingOrder.value || cartIsEmpty.value)

const addToFavorite = (item) => {
  if (!item.favoriteId) {
    const obj = {
      parentId: item.id,
      item,
    }

    item.isFavorite = true

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
  } else {
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
  }
}

provide('favorite', { addToFavorite })

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

const createOrder = () => {
  const obj = {
    items: cart.value,
    totalPrice: totalPrice.value,
  }

  isCreatingOrder.value = true

  fetch(`https://f6f031af57a38201.mokky.dev/orders`, {
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
    .then(() => {
      cart.value = []
    })
    .catch((error) => {
      console.error('Ошибка при отправке данных в orders:', error)
    })
    .finally(() => {
      isCreatingOrder.value = false
    })
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
    <div class="grid grid-rows-[auto_1fr] bg-white min-h-screen rounded-3xl">
      <AppHeader @onClickOpenCart="onClickOpenCart" :totalPrice="totalPrice" />

      <main>
        <router-view></router-view>
      </main>

      <Drawer
        v-if="cartOpen"
        :totalPrice="totalPrice"
        @onClickCloseCart="onClickCloseCart"
        @createOrder="createOrder"
        :disabledButton="disabledButton"
        :isCreatingOrder="isCreatingOrder.value"
      />
    </div>
  </div>
</template>

<style scoped></style>
