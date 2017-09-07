<template>
 <div class="cartcontrol">
   <transition name="fade">
     <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="removecart">
       <span class="inner icon-remove_circle_outline"></span>
     </div>
   </transition>

  <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
  <div class="cart-add icon-add_circle" @click.stop.prevent="addcart"></div>
 </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';
 export default {
   props: {
     food: {
       type: Object
     }
   },
    methods: {
      addcart (event) {
     if (!event._constructed) {
       return;
     }
     console.log(1);
     if (!this.food.count) {
       Vue.set(this.food, 'count', 1);
     } else {
       this.food.count++;
     }
   },
  removecart (event) {
    if (!event._constructed) {
      return;
    }
    console.log(1);
    if (this.food.count) {
      this.food.count--;
    } else {
      Vue.set(this.food, 'count', 0);
    }
  }
 }
 };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    .cartcontrol
      font-size: 0
      .cart-decrease
        display: inline-block
        padding: 6px
        .inner
          line-height: 24px
          font-size: 24px
          color: rgb(0, 160, 220)
          transform: rotate(0deg)
        &.fade-enter-active
          opacity: 1
          transform: translate3d(0, 0, 0)
          transition: all 0.4s linear
        &.fade-leave-active
           opacity: 1
           transform: translate3d(0, 0, 0)
           transition: all 0.4s linear
        &.fade-enter
          transform: rotate(180deg)
          opacity: 0
        &.fade-leave-to
          transform: rotate(180deg)
          opacity: 0
      .cart-count
        display: inline-block
        vertical-align: top
        width: 12px
        padding-top: 6px
        line-height: 24px
        text-align: center
        font-size: 10px
        color: rgb(147, 153, 159)
      .cart-add
        display: inline-block
        padding: 6px
        line-height: 24px
        font-size: 24px
        color: rgb(0, 160, 220)
</style>
