<template>
  <div class="cartcontrol">
      <div v-show="food.count > 0" class="cart-decrease" @click.stop="decreaseCart">
      </div>
      <div v-show="food.count" class="cart-count">
          {{food.count}}
      </div>
      <div class="cart-add" @click.stop="addCart">
      </div>
  </div>
</template>

<script>
import Vue from 'vue';
// import { eventBus } from '../event-bus.js';

export default {
    props: {
        food: {
            type: Object,
            default () {
                return [
                    {
                        price: 0,
                        count: 0
                    }
                ];
            }
        }
    },
    methods: {
        addCart () {
            if (!this.food.count) {
                Vue.set(this.food, 'count', 1);
            } else {
                this.food.count++;
            }
            // eventBus.$emit('cart.add', event.target);
        },
        decreaseCart () {
            if (this.food.count) {
                this.food.count--;
            }
        }
    }
};
</script>

<style scoped>
.cartcontrol{
    font-size: 0;
}
    .cart-decrease, .cart-add{
        display: inline-block;
        width: 24px;
        height: 24px;
        padding: 6px;
        background-size: 24px;
        background-position: center;
        background-repeat: no-repeat;
        line-height: 24px;
        font-size: 24px;
    }
    .cart-decrease{
        background-image: url('remove_circle_outline.png');
    }
    .cart-count{
        display: inline-block;
        vertical-align: top;
        width: 12px;
        padding-top: 6px;
        line-height: 24px;
        text-align: center;
        font-size: 10px;
        color: rgb(147, 153, 159);
    }
    .cart-add{
        background-image: url('add_circle.png');
    }
</style>
