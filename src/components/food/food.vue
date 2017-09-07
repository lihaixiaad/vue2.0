<template>
  <transition name="fade">
    <div class="food" v-show="showFlag" ref="food">
      <div class="food-content">
        <div class="img-header">
          <img :src="food.image">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food"></cartcontrol>
          </div>
          <transition name="fade">
            <div @click.stop.prevent="addFood" class="buy" v-show="!food.count || food.count===0">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品介绍</h1>
          <p class="detail">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingselect :select-type="selectType" :only-content="onlyContent" :desc="desc" :ratings="food.ratings"
                        @select="selectFood" @toggle="toggleFood"></ratingselect>
           <div class="rating-wrapper">
             <ul v-show="food.ratings && food.ratings.length">
               <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item border-1px">
                 <div class="user">
                   <span class="username">{{rating.username}}</span>
                   <img :src="rating.avatar" class="avatar" width="12" height="12">
                 </div>
                 <div class="time">{{rating.rateTime | formatDate}}</div>
                 <p class="text">
                   <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>
                  {{rating.text}}
                 </p>
               </li>
             </ul>
             <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
           </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import Vue from 'vue';
  import {formatDate} from 'common/js/date';
  import cartcontrol from 'components/cartcontrol/cartcontrol';
  import ratingselect from 'components/ratingselect/ratingselect';
  import split from 'components/split/split';

  const ALL = 2;
//  const eventhub = new vue();

  export default {
    props: {
      food: {
        type: Object
      }
    },
    data () {
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc: {
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    };
  },
  methods: {
    show () {
      this.showFlag = true;
      this.selectType = ALL;
      this.onlyContent = false;
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.food, {
            click: true
          });
        } else {
          this.scroll.refresh();
        }
      });
    },
    hide () {
      this.showFlag = false;
    },
    addFood (event) {
      if (!event._constructed) {
        return;
      }
      this.$emit('cart.add', event.target);
      Vue.set(this.food, 'count', 1);
    },
    needShow (type, text) {
      if (this.onlyContent && !text) {
        return false;
      }
      if (this.selectType === ALL) {
        return true;
      } else {
        return type === this.selectType;
      }
    },
    selectFood (type) {
      this.selectType = type;
      this.$nextTick(() => {
        this.scroll.refresh();
      });
    },
    toggleFood () {
      this.onlyContent = !this.onlyContent;
      this.$nextTick(() => {
        this.scroll.refresh();
      });
    }
  },
  filters: {
    formatDate (time) {
      let date = new Date(time);
      return formatDate(date, 'yyyy-MM-dd hh:mm');
    }
  },
  components: {
    cartcontrol,
      ratingselect,
      split
  }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .food
    position: fixed
    width: 100%
    left: 0
    top: 0
    bottom: 48px
    background: #fff
    &.fade-enter-active, &.fade-leave-active
      transition: all 0.3s linear
      transform: translate3d(0, 0, 0)
    &.fade-enter, &.fade-leave-to
      transform: translate3d(100%, 0, 0)
    .img-header
      position: relative
      width: 100%
      height: 0
      padding-top: 100%
      img
       position: absolute
       top: 0
       left: 0
       width: 100%
       height: 100%
      .back
        position: absolute
        top: 10px
        left: 0
        padding: 10px
        font-size: 20px
        color: #fff
    .content
      position: relative
      padding: 18px
      font-size: 0
      .title
        padding-bottom: 8px
        line-height: 14px
        font-size: 14px
        font-weight: 700
        color: rgb(7, 17, 27)
      .detail
        margin-bottom: 18px
        color: rgb(127, 153, 159)
        line-height: 10px
        height: 10px
        font-size: 10px
        .sell
          padding-right: 12px
      .price
        line-height: 24px
        font-weight: 700
        .now
          margin-right: 8px
          color: rgb(240, 20, 20)
          font-size: 14px
        .old
          color: rgb(147, 153, 159)
          font-size: 10px
      .cartcontrol-wrapper
        position: absolute
        right: 12px
        bottom: 12px
      .buy
        position: absolute
        right: 18px
        bottom: 18px
        padding: 0 12px
        height: 24px
        line-height: 24px
        border-radius: 12px
        box-sizing: border-box
        color: #fff
        background: rgb(0, 160, 220)
        font-size: 10px
        z-index: 10
        &.fade-enter-active, &.fade-leave-active
          opacity: 1
          transition: all 0.5s
        &.fade-enter, &.fade-leave-to
          opacity: 0
    .info
      padding: 18px
      .title
        padding-bottom: 6px
        line-height: 14px
        font-size: 14px
        color: rgb(7, 17, 27)
      .detail
        font-size: 12px
        padding: 0 8px
        font-weight: 200
        color: rgb(77, 85, 93)
        line-height: 24px
    .rating
      padding-top: 18px
      .title
        margin-left: 18px
        line-height: 14px
        font-size: 14px
        color: rgb(7, 17, 27)
      .rating-wrapper
        padding: 0 18px
        .rating-item
          position: relative
          padding: 18px 0
          color: rgb(147, 153, 159)
          border-1px(rgba(7, 17, 27,0.1))
          .time
            font-size: 10px
            line-height: 12px
            margin-bottom: 6px
          .user
            position: absolute
            right: 0
            top: 16px
            font-size: 0
            line-height: 12px
            .username
              display: inline-block
              vertical-align: top
              font-size: 10px
            .avatar
              display: inline-block
              border-radius: 50%
              margin-left: 6px
          .text
            display: inline-block
            font-size: 12px
            line-height: 16px
            color: rgb(7, 17, 27)
            .icon-thumb_down
              font-size: 10px
              line-height: 16px
              margin-right: 4px
              color: rgb(147, 153, 159)
            .icon-thumb_up
              font-size: 10px
              line-height: 16px
              margin-right: 4px
              color: rgb(0, 160, 220)
        .no-rating
          padding: 16px 0
          font-size: 12px
          color: rgb(147, 153, 159)
</style>
