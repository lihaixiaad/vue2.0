<template>
<div class="goods">
  <div class="menu-wrapper" ref="menuWrapper">
    <ul class="son">
      <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}"
          @click="selectmenu(index, $event)">
        <span class="icon" v-if="item.type>0" :class="classMap[item.type]"></span>
        <span class="text border-1px">{{item.name}}</span>
      </li>
    </ul>
  </div>
  <div class="food-wrapper" ref="foodWrapper">
    <ul>
      <li v-for="item in goods" class="food-list food-list-hook">
        <h1 class="title">{{item.name}}</h1>
        <ul>
          <li @click="selectFood(food,  $event)" v-for="food in item.foods" class="food-item border-1px">
            <div class="icon">
              <img width="57" height="57" :src="food.icon">
            </div>
            <div class="content">
              <h2 class="name">{{food.name}}</h2>
              <p class="desc">{{food.description}}</p>
              <div class="extra">
                <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
              </div>
              <div class="price">
                <span class="now">￥{{food.price}}</span>
                <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>

            </div>
          </li>
        </ul>
      </li>
      </ul>
  </div>
  <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
  <food :food="selectedFood" ref="food" @add="addFood"></food>
</div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from 'components/shopcart/shopcart';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import food from 'components/food/food';
export default{
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    shopcart,
    cartcontrol,
      food
  },
  data () {
  return {
    goods: [],
    listHeight: [],
    scrollY: 0,
    selectedFood: {}
  };
  },
  created () {
  this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  this.$http.get('/api/goods').then((response) => {
    response = response.data;
    this.goods = response.data;
    console.log(this.goods);
    this.$nextTick(() => {
      this._initScroll();
      this._calculateHeight();
    });
  });
},
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        }
      }
      return 0;
    },
    selectFoods () {
      let foods = [];
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food);
          }
        });
      });
      return foods;
    }
  },
  methods: {
    selectFood (food, event) {
      if (!event._constructed) {
        return;
      }
      this.selectedFood = food;
      this.$refs.food.show();
    },
    selectmenu (index, event) {
      if (!event._constructed) {
        return;
      }
      let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
      let el = foodList[index];
      this.foodScroll.scrollToElement(el, 300);
    },
    _initScroll () {
      this.meunScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.foodScroll = new BScroll(this.$refs.foodWrapper, {
        click: true,
        probeType: 3
      });
      this.foodScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight () {
      let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }
    },
    addFood (target) {
      this._drop(target);
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
    .goods
      display: flex
      position: absolute
      top: 174px
      bottom: 46px
      width: 100%
      overflow: hidden
      .menu-wrapper
        flex: 0 0 80px
        width: 80px
        background: #f3f5f7
        .menu-item
          display: table
          height: 54px
          width: 56px
          padding: 0 12px
          line-height: 14px
          &.current
            position: relative
            z-index: 10
            margin-top: -1px
            background: #fff
            font-weight: 700
            .text
              border-none()
          .icon
            display: inline-block
            vertical-align: top
            width: 12px
            height: 12px
            margin-right: 2px
            background-size: 12px 12px
            background-repeat: no-repeat
            &.decrease
              bg-image('decrease_3')
            &.discount
              bg-image('discount_3')
            &.guarantee
              bg-image('guarantee_3')
            &.invoice
              bg-image('invoice_3')
            &.special
              bg-image('special_3')
          .text
            display: table-cell
            vertical-align: middle
            font-size: 12px;
            width: 56px
            border-1px(rgba(7, 17, 27, 0.1))
      .food-wrapper
        flex: 1
        height: 100%
        .title
          height: 26px
          width: 100%
          background: #f3f5f7
          font-size: 12px
          line-height: 26px
          padding-left: 14px
          color: rgb(147, 153, 159)
          border-left: 2px solid #d9dde1
        .food-item
          display: flex
          margin: 18px
          padding-bottom: 18px
          border-1px(rgba(7, 17, 27, 0.1))
          &:last-child
            border-none()
            margin-bottom: 0
          .icon
            flex: 0 0 57px
            margin-right: 10px
          .content
            flex: 1
            width: 100%
            height: 100%
            .name
              margin: 2px 0 8px 0
              height: 14px
              line-height: 14px
              font-size: 14px
              color: rgb(7, 17, 27)
            .desc, .extra
              line-height: 10px
              font-size: 10px
              color: rgb(147, 153, 159)
            .desc
              margin-bottom: 8px
            .extra
              .count
                margin-right: 12px
            .price
              font-weight: 700
              line-height: 24px
              .now
                font-size: 14px
                color: rgb(240, 20, 20)
                padding-right: 8px
              .old
                font-size: 10px
                text-decoration: line-through
                color: rgb(147, 153, 159)
            .cartcontrol-wrapper
              position: absolute
              right: 0
              bottom: 12px

</style>
