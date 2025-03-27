<script setup>
import debounce from '@/utils/debounce.js'

import { inject, reactive, watch, ref, onMounted } from 'vue'
import CardList from '../components/CardList.vue'

const items = ref([])
const { cart, onClickAddPlus } = inject('cart')
const { onClickButtonFavorite } = inject('favorite')

const filters = reactive({
  sortBy: '',
  searchQuery: '',
})

const onChangeSearchInput = debounce((evt) => {
  filters.searchQuery = evt.target.value
})

const onChangeSort = (evt) => {
  filters.sortBy = evt.target.value
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

watch(filters, fetchItems)
</script>

<template>
  <!-- <div class="hero"></div> -->

  <section class="py-10">
    <div class="mx-auto px-20">
      <header class="flex items-center justify-between gap-4 mb-8">
        <h2 class="text-3xl font-bold">Все кроссовки</h2>

        <div class="flex flex-wrap items-center justify-end gap-8">
          <select @change="onChangeSort" aria-label="Сортировка">
            <option selected value="name">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>

          <label
            class="flex items-center gap-3 border border-slate-200 rounded-xl p-4 focus-within:outline focus-within:outline-lime-500 cursor-pointer hover:opacity-75 active:opacity-50 text-slate-500 hover:text-slate-600 active:text-slate-700 transition-all text-sm font-medium"
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

      <CardList
        :items="items"
        @on-click-button-favorite="onClickButtonFavorite"
        @add-to-cart="onClickAddPlus"
      />
    </div>
  </section>
</template>
