<template>
  <div class="shopcart">
    <div class="shopcarts">
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'heightlight': totalCount>0}">
              <i class="icon-shopping_cart" :class="{'heightlight': totalCount>0}"></i>
            </div>
            <div class="number" v-show="totalCount>0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{'heightlight': totalCount>0}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right">
          <div class="text" :class="payClass" @click.stop.prevent="pay">
            {{payDesc}}
          </div>
        </div>
      </div>
      <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref="listContent">
          <ul>
            <li class="food" v-for="food in selectFoods">
              <span class="name">{{food.name}}111</span>
              <div class="price">
                <span>￥{{food.price*food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="close"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import BScroll from 'better-scroll';
 export default {
   props: {
     selectFoods: {
       type: Array,
       default () {
       return [];
     }
     },
     deliveryPrice: {
       type: Number,
       default: 0
     },
     minPrice: {
       type: Number,
       default: 0
     }
   },
   data () {
     return {
       fold: true
     };
   },
   methods: {
     toggleList () {
       if (!this.totalCount) {
         return;
       }
       this.fold = !this.fold;
     },
     close () {
       this.fold = true;
     },
     pay () {
       if (this.totalPrice < this.minPrice) {
         return;
       }
       window.alert(`支付${this.totalPrice}元`);
     },
     empty () {
       this.selectFoods.forEach((food) => {
         food.count = 0;
       });
     },
     _initScroll () {
       this.cartScroll = new BScroll(this.$refs.listContent, {
         click: true
       });
     }
  },
 computed: {
   listShow () {
     if (!this.totalCount) {
       this.fold = true;
       return false;
     }
     let show = !this.fold;
     if (show) {
         this.$nextTick(() => {
           if (!this.cartScroll) {
             this._initScroll();
           } else {
             this.cartScroll.refresh();
         }
       });
     }
     return show;
   },
   totalPrice () {
     let total = 0;
     this.selectFoods.forEach((food) => {
       total += food.price * food.count;
     });
     return total;
   },
   totalCount () {
     let count = 0;
     this.selectFoods.forEach((food) => {
       count += food.count;
     });
     return count;
   },
  payDesc () {
     if (this.totalPrice === 0) {
       return `￥${this.minPrice}元起送`;
       } else if (this.totalPrice < this.minPrice) {
       let least = this.minPrice - this.totalPrice;
       return `还差￥${least}元起送`;
     } else {
       return '去结算';
     }
   },
   payClass () {
     if (this.totalPrice < this.minPrice) {
       return 'not-enough';
     } else {
       return 'enough';
     }
   }
 },
   components: {
     cartcontrol
   }
 };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .shopcart
    .shopcarts
      position: fixed
      left: 0
      bottom: 0
      z-index: 50
      width: 100%
      height: 48px
      .content
       display: flex
       background: #141d27
       font-size: 0
       .content-left
         flex: 1
         .logo-wrapper
           display: inline-block
           position: relative
           top: -10px
           background: #141d27
           width: 56px
           height: 56px
           margin: 0 16px
           padding: 6px
           box-sizing: border-box
           border-radius: 50%
           vertical-align: top
           .logo
             width: 100%
             height: 100%
             background: #2b343c
             border-radius: 50%
             text-align: center
             &.heightlight
               background: rgb(0, 160, 220)
             .icon-shopping_cart
               font-size: 24px
               line-height: 44px
               color: #80858a
               &.heightlight
                 color: #fff
           .number
              position: absolute
              right: 0
              top: 0;
              background: red
              width: 24px
              height: 16px
              border-radius: 16px
              font-size: 9px
              line-height: 16px
              font-weight: 700
              text-align: center
              color: #fff
              box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.4)
         .price
           display: inline-block
           vertical-align: top
           font-size: 16px
           font-weight: 700
           line-height: 24px
           color: rgba(255, 255, 255, 0.4)
           margin-top: 12px
           padding-right: 12px
           box-sizing: border-box
           border-right: 1px solid rgba(255, 255, 255, 0.1)
           &.heightlight
             color: #fff
         .desc
           display: inline-block
           vertical-align: top
           font-size: 16px
           font-weight: 700
           line-height: 24px
           color: rgba(255, 255, 255, 0.4)
           margin: 12px 0 0 12px
       .content-right
          flex: 0 0 105px
          width: 105px
          background: #2b333b
          .text
            font-size: 12px
            font-weight: 700
            line-height: 48px
            height: 48px
            color: rgba(255, 255, 255, 0.4)
            text-align: center
            &.not-enough
              background: #2b333b
            &.enough
              background: #00b43c
              color: #fff
      .shopcart-list
         position: absolute
         left: 0
         bottom: 48px
         width: 100%
         z-index: -1
         transition: all 0.5s
         &.fold-enter-to, &.fold-leave
           transition: all 0.3s linear
           transform: translate3d(0, 0, 0)
         &.fold-enter, &.fold-leave-to
           transform: translate3d(0, 100%, 0)
         .list-header
           height: 40px
           line-height: 40px
           background: #f3f5f7
           padding: 0 18px
           border-bottom: 2px solid rgba(7, 17, 27, 0.1)
           .title
             float: left
             font-size: 14px
             color: rgb(7, 17, 27)
           .empty
             float: right
             font-size: 12px
             color: rgb(0, 160, 220)
         .list-content
            padding: 0 18px
            max-height: 217px
            overflow: hidden
            background: #fff
            .food
              position: relative
              padding: 12px 0
              box-sizing: border-box
              border-1px(rgba(7, 17, 27, 0.1))
              .name
                line-height: 24px
                font-size: 14px
                color: rgb(7, 17, 27)
              .price
                position: absolute
                right: 90px
                bottom: 12px
                line-height: 24px
                font-size: 14px
                font-weight: 700
                color: rgb(240, 20, 20)
              .cartcontrol-wrapper
                position: absolute
                right: 0
                bottom: 6px
    .list-mask
       position: fixed
       left: 0
       bottom: 0
       width: 100%
       height: 100%
       z-index: 40
       background: rgba(7, 17, 27, 0.6)
       backdrop-filter: blur(10px)
       &.fade-enter-active, &.fade-leave-active
         transition: all 0.5s
         opacity: 1
       &.fade-enter, &.fade-leave-to
         background: rgba(7, 17, 27, 0)
         opacity: 0





</style>
