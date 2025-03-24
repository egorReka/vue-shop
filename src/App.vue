<script setup>
import { ref, reactive, onMounted, watch, provide, computed } from 'vue'

import AppHeader from './components/AppHeader.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([])
const cart = ref([])
const cartOpen = ref(false)
const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const isCreatingOrder = ref(false)

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

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
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

const cartIsEmpty = computed(() => cart.value.length === 0)
const disabledButton = computed(() => isCreatingOrder.value || cartIsEmpty.value)

provide('cart', {
  cart,
  addToCart,
  removeFromCart,
})

const filters = reactive({
  sortBy: '',
  searchQuery: '',
})

const onChangeSort = (evt) => {
  filters.sortBy = evt.target.value
}

const onChangeSearchInput = (evt) => {
  filters.searchQuery = evt.target.value
}

const fatchFavorites = () => {
  fetch(`https://f6f031af57a38201.mokky.dev/favorites`)
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка HTTP: ${response.status}`)
      }

      return response.json()
    })
    .then((favorites) => {
      items.value = items.value.map((item) => {
        const favorite = favorites.find((favorite) => favorite.parentId === item.id)

        if (!favorite) {
          return item
        }

        return {
          ...item,
          isFavorite: true,
          favoriteId: favorite.id,
        }
      })
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

const addToFavorite = (item) => {
  if (!item.favoriteId) {
    const obj = {
      parentId: item.id,
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

const fetchItems = () => {
  const params = {
    sortBy: filters.sortBy,
    searchQuery: filters.searchQuery,
  }

  const getQueryString = (params) => {
    if (params.searchQuery && params.sortBy) {
      return `?title=*${params.searchQuery}*&sortBy=${params.sortBy}`
    }

    if (params.searchQuery) {
      return `?title=*${params.searchQuery}*`
    }

    if (params.sortBy) {
      return `?sortBy=${params.sortBy}`
    }

    return ''
  }

  fetch(`https://f6f031af57a38201.mokky.dev/items${getQueryString(params)}`)
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка HTTP: ${response.status}`)
      }

      return response.json()
    })
    .then((data) => {
      items.value = data.map((item) => ({
        ...item,
        isAdded: cart.value.some((cartItem) => cartItem.id === item.id),
        isFavorite: false,
        favoriteId: null,
      }))
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fatchFavorites()
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }))
})

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: true },
)

watch(filters, fetchItems)
</script>

<template>
  <div class="bg-[#E7F6FF] p-10">
    <div class="grid grid-rows-[auto_1fr] bg-white min-h-screen rounded-3xl">
      <AppHeader @onClickOpenCart="onClickOpenCart" :totalPrice="totalPrice" />

      <main>
        <!-- <div class="hero"></div> -->

        <section class="py-10">
          <div class="mx-auto px-20 max-w-7xl w-full">
            <header class="flex items-center justify-between gap-4 mb-8">
              <h2 class="text-3xl font-bold">Все кроссовки</h2>

              <div class="flex items-center gap-8">
                <select @change="onChangeSort" aria-label="Сортировка">
                  <option selected value="name">По названию</option>
                  <option value="price">По цене (дешевые)</option>
                  <option value="-price">По цене (дорогие)</option>
                </select>

                <label
                  class="flex items-center gap-3 border border-slate-200 rounded-xl p-4 focus-within:outline focus-within:outline-2 focus-within:outline-lime-500 cursor-pointer hover:opacity-75 active:opacity-50 transition-opacity text-slate-500 hover:text-slate-600 active:text-slate-700 transition-all text-sm font-medium"
                >
                  <img class="event-none" src="/search.svg" width="16" height="16" alt="Поиск." />
                  <input
                    @input="onChangeSearchInput"
                    class="appearance-none outline-none"
                    type="text"
                    placeholder="Поиск..."
                  />
                </label>
              </div>
            </header>

            <CardList :items="items" @addToFavorite="addToFavorite" @addToCart="onClickAddPlus" />
          </div>
        </section>
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
