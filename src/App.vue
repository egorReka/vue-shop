<script setup>
import { ref, reactive, onMounted, watch } from 'vue'

import AppHeader from './components/AppHeader.vue'
import CardList from './components/CardList.vue'
import Drawer from './components/Drawer.vue'

const items = ref([])

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
      items.value = data
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

onMounted(fetchItems)
watch(filters, fetchItems)
</script>

<template>
  <div class="bg-[#E7F6FF] p-10">
    <div class="grid grid-rows-[auto_1fr] bg-white min-h-screen rounded-3xl">
      <AppHeader />

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

            <CardList :items="items" />
          </div>
        </section>
      </main>

      <!-- <Drawer /> -->
    </div>
  </div>
</template>

<style scoped></style>
