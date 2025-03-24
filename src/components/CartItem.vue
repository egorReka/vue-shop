<script setup>
defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number,
})

const emmit = defineEmits(['onClickRemove'])
</script>

<template>
  <div class="flex items-center gap-4 border border-slate-200 rounded-xl p-4">
    <img class="min-w-18 h-18" :src="imageUrl" width="70" height="70" :alt="title" />

    <div>
      <h3 class="mb-2">{{ title }}</h3>
      <div class="flex items-center justify-between gap-4">
        <p class="text-lg font-bold">{{ price }} руб</p>
        <button
          class="opacity-40 hover:opacity-100 active:opacity-60 cursor-pointer transition-opacity"
          type="button"
          @click="() => emmit('onClickRemove')"
        >
          <img src="/close.svg" alt="Удалить." />
        </button>
      </div>
    </div>
  </div>
</template>
