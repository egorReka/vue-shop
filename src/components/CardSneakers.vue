<script setup>
defineProps({
  title: String,
  imageUrl: String,
  price: Number,
  isFavorite: Boolean,
  isProcessing: Boolean,
  isAdded: Boolean,
  onClickAdd: Function,
  onClickButtonFavorite: Function,
})
</script>

<template>
  <div
    class="relative grid items-start content-start h-full max-w-[400px] border border-slate-100 rounded-3xl p-8 hover:-translate-y-2 transition-all hover:shadow-2xl"
  >
    <button
      v-if="onClickButtonFavorite"
      :disabled="isProcessing"
      @click="onClickButtonFavorite"
      class="absolute top-8 left-8 cursor-pointer"
      type="button"
    >
      <img
        :src="isFavorite ? '/like-2.svg' : '/like-1.svg'"
        width="32"
        height="32"
        :alt="isFavorite ? 'Добавить в закладки.' : 'Удалить из закладок.'"
      />
    </button>

    <img :src="imageUrl" width="133" height="112" alt="Кросовки Nike." />
    <h3 class="mb-5">{{ title }}</h3>

    <div @click="onClickAdd" class="flex justify-between items-start">
      <dl>
        <dt class="text-sm text-slate-400 uppercase">Цена:</dt>
        <dd class="text-sm font-bold">{{ price }} руб.</dd>
      </dl>

      <button class="cursor-pointer" type="button">
        <img
          :src="isAdded ? '/checked.svg' : '/plus.svg'"
          width="32"
          height="32"
          :alt="isAdded ? 'Добавить в корзину.' : 'Удалить из корзины.'"
        />
      </button>
    </div>
  </div>
</template>
