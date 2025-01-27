<script setup>
import CartItemList from './CartItemList.vue';
import DrawerHead from './DrawerHead.vue';
import InfoBlock from './InfoBlock.vue';


defineProps({
  totalPrice : Number,
  vatPrice: Number,
  cartButtonDisabled: Boolean,
})

const emit = defineEmits(['createOrder'])


</script>
<template>
  <div class="fixed top-0
   left-0 h-full w-full
    bg-black
     z-10 opacity-70">
    <div
     class="bg-white
     w-96 h-full fixed right-0 top-0 z-20 p-8">
      <DrawerHead  />

      <div v-if="!totalPrice" class="flex h-full items-center">
        <InfoBlock  title="Корзина пустая" description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ." image-url="/package-icon.png" />
      </div>

      <div class="v-else">
        <CartItemList />
        <div v-if="totalPrice" class="flex flex-col gap-4 my-7">
          <div class="flex gap-2">
            <span>Итого:</span>
            <div class="flex-1 border-b border-dashed"></div>
            <b>{{totalPrice}} Р</b>
          </div>
          <div class="flex gap-2">
            <span>Налог 5%:</span>
            <div class="flex-1 border-b border-dashed"></div>
            <b>{{vatPrice}} Р</b>
          </div>

          <button
          @click="() => emit('createOrder')"
          :disabled="cartButtonDisabled"
            class="mt-4 transition bg-lime-500 w-full rounded-xl py-3 hover:bg-lime-600 disabled:bg-slate-300 active:bg-lime-700">Оформить заказ</button>

        </div>
      </div>


    </div>
  </div>
</template>
