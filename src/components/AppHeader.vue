<script setup>
const emmit = defineEmits(['onClickOpenCart'])

defineProps({
  totalPrice: Number,
})
</script>

<template>
  <header class="flex justify-between items-center py-4 border-b border-slate-300">
    <div class="flex justify-between items-center gap-4 mx-auto px-20 max-w-7xl w-full">
      <div class="grid grid-cols-[auto_1fr] items-center gap-4">
        <img
          class="block max-w-full h-auto min-w-10 h-10"
          src="/logo.png"
          width="40"
          height="40"
          alt="Logo."
        />
        <div>
          <p class="text-xl font-bold uppercase">Vue Sneakers</p>
          <h1 class="text-slate-400">Магазин лучших кроссовок</h1>
        </div>
      </div>

      <ul class="flex gap-10 items-center">
        <li>
          <button
            @click="() => emmit('onClickOpenCart')"
            class="flex items-center gap-4 px-2 py-4 text-xl fount-medium text-gray-600 hover:opacity-75 active:opacity-50 transition-opacity cursor-pointer"
            type="button"
          >
            <img src="/cart.svg" width="18" height="18" alt="Корзина." />
            <b>{{ totalPrice }}</b>
          </button>
        </li>
        <li>
          <a
            class="flex items-center gap-4 px-2 py-4 text-xl fount-medium text-gray-600 hover:opacity-75 active:opacity-50 transition-opacity"
            href="#"
          >
            <img src="/heart.svg" width="18" height="18" alt="Закладки." />
            <b>Закладки</b>
          </a>
        </li>
        <li>
          <a
            class="flex items-center gap-4 px-2 py-4 text-xl fount-medium text-gray-600 hover:opacity-75 active:opacity-50 transition-opacity"
            href="#"
          >
            <img src="/profile.svg" width="18" height="18" alt="Профиль." />
            <b>Профиль</b>
          </a>
        </li>
      </ul>
    </div>
  </header>
</template>
