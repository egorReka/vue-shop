<script setup>
import CartList from './CartList.vue'
import infoBlock from './infoBlock.vue'

const emmit = defineEmits(['onClickCloseCart', 'createOrder'])

const props = defineProps({
  totalPrice: Number,
  isCreatingOrder: Boolean,
  disabledButton: Boolean,
})
</script>

<template>
  <div
    @click="() => emmit('onClickCloseCart')"
    class="fixed top-0 left-0 h-full w-full bg-black opacity-50 z-10"
  ></div>

  <aside
    class="grid grid-rows-[auto_1fr_auto] items-start h-full fixed top-0 right-0 h-full w-96 bg-white z-20 p-8"
  >
    <header class="flex items-center gap-4 mb-8">
      <button
        @click="() => emmit('onClickCloseCart')"
        class="bg-black p-4 rounded-xl cursor-pointer hover:opacity-75 active:opacity-50 transition-opacity"
        type="button"
      >
        <img
          class="rotate-180"
          src="/arrow-next.svg"
          width="14"
          height="12"
          alt="Закрыть корзину."
        />
      </button>
      <h2 class="text-3xl font-bold">Корзина</h2>
    </header>

    <CartList v-if="totalPrice" />

    <infoBlock
      v-if="!totalPrice"
      title="Корзина пустая"
      description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
      image-url="/package-icon.png"
    />

    <footer v-if="totalPrice" class="self-end">
      <dl class="grid gap-2 mb-5">
        <div
          class="flex before:content-[''] items-baseline gap-1 before:w-full before:border-b before:border-dashed before:border-slate-300"
        >
          <dt class="order-first min-w-max text-lg">Итого:</dt>
          <dd class="order-last min-w-max text-lg font-bold">{{ totalPrice }}руб.</dd>
        </div>

        <div
          class="flex before:content-[''] items-baseline gap-1 before:w-full before:border-b before:border-dashed before:border-slate-300"
        >
          <dt class="order-first min-w-max text-lg">Налог 5%:</dt>
          <dd class="order-last min-w-max text-lg font-bold">
            {{ Math.round((totalPrice * 5) / 100) }} руб.
          </dd>
        </div>
      </dl>

      <button
        :disabled="disabledButton"
        @click="() => emmit('createOrder')"
        class="grid grid-cols-[1fr_auto] w-full bg-lime-500 p-4 rounded-xl flex items-center gap-4 text-lg font-bold text-white disabled:bg-slate-400 disabled:cursor-default hover:bg-lime-600 active:bg-lime-700 transition-colors cursor-pointer"
        type="button"
      >
        <span>Оформить заказ</span>
        <img src="/arrow-next.svg" width="14" height="12" alt="" />
      </button>
    </footer>
  </aside>
</template>
