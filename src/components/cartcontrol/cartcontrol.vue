<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="minusCart">
        <i class="icon-remove_circle_outline"></i>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">
      {{food.count}}
    </div>
    <div class="cart-increase icon-add_circle" @click.stop.prevent="addCart"></div>
  </div>
</template>

<script type='text/ecmascript-6'>
  import Vue from 'vue';

  export default {
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      addCart(event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$emit('add', event.target);
      },
      minusCart() {
        if (this.food.count === 0) {
          Vue.set(this.food, 'count', 0);
        } else {
          this.food.count--;
        }
      }
    }
  };
</script>

<style lang="scss" rel="stylesheet/scss">
  .cartcontrol{
    font-size: 0;
    .cart-decrease{
      display: inline-block;
      padding: 6px;
      &.move-enter-active,&.move-leave-active{
        transition: all 0.4s linear;
      }
      &.move-enter,&.move-leave-to{
        opacity: 0;
        transform: translate3d(24px,0,0) rotate(180deg);
      }
      .icon-remove_circle_outline{
        display: inline-block;
        line-height: 24px;
        font-size: 24px;
        color: rgb(0,160,200);
      }
    }
    .cart-increase{
      display: inline-block;
      padding: 6px;
      line-height: 24px;
      font-size: 24px;
      color: rgb(0,160,200);
    }
    .cart-count{
      display: inline-block;
      vertical-align: top;
      width: 12px;
      padding-top: 6px;
      line-height: 24px;
      text-align: center;
      font-size: 10px;
      color: rgb(147,153,159);
    }
  }
</style>
