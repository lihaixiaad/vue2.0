<template>
  <div class="seller" ref="sellers">
    <div class="seller-content">
       <div class="overview">
         <h1 class="sellname">{{seller.name}}</h1>
         <div class="star-wrapper border-1px">
           <star :size="36" :score="seller.score" class="star"></star>
           <span class="ratingcount">({{seller.ratingCount}})</span>
           <span class="sellcount">月售{{seller.sellCount}}单</span>
         </div>
         <div class="collected" @click="togglecollect">
           <div class="icon-favorite" :class="{'active': collected}"></div>
           <div class="text">{{collectedtext}}</div>
         </div>
         <ul class="remark">
             <li class="block">
               <h1>起送价</h1>
               <div class="content">
                 <span class="streng">{{seller.minPrice}}</span>元
               </div>
             </li>
             <li class="block">
               <h1>商家配送</h1>
               <div class="content">
                 <span class="streng">{{seller.deliveryPrice}}</span>元
               </div>
             </li>
             <li class="block">
               <h1>平均配送时间</h1>
               <div class="content">
                 <span class="streng">{{seller.deliveryTime}}</span>分钟
               </div>
             </li>
           </ul>
       </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-1px">
          <div class="content">{{seller.bulletin}}</div>
        </div>
      </div>
      <ul v-if="seller.supports" class="supports">
        <li class="support-item border-1px" v-for="(item,index) in seller.supports">
          <span class="icon" :class="classMap[seller.supports[index].type]"></span>
          <span class="text">{{seller.supports[index].description}}</span>
        </li>
      </ul>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="pic-wrapper">
          <ul class="pic-list" ref="pic-list">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90"/>
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1>商家信息</h1>
        <ul>
          <li v-for="info in seller.infos" class="info-item">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import star from 'components/star/star';
  import {saveToLocal, loadFromLocal} from 'common/js/store';
  import split from 'components/split/split';
  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
    return {
      collected: (() => {
        return loadFromLocal(this.seller.id, 'collected', false);
      })()
    };
  },
    computed: {
      collectedtext () {
        return this.collected ? '已收藏' : '收藏';
      }
    },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  watch: {
    'seller' () {
      this._initScroll();
      this._initPics();
    }
  },
  mounted: function () {
    this.$nextTick(function () {
      this._initScroll();
    this._initPics();
    });
  },
  methods: {
    togglecollect (event) {
      if (!event._constructed) {
        return;
      }
      this.collected = !this.collected;
      saveToLocal(this.seller.id, 'collected', this.collected);
    },
    _initScroll () {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.sellers, {
          click: true
        });
      } else {
        this.scroll.refresh();
      }
    },
    _initPics () {
      let picWidth = 120;
      let margin = 6;
      let width = (picWidth + margin) * this.seller.pics.length - margin;
      this.$refs.picList.style.width = width + 'px';
      this.$nextTick(() => {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.picWrapper, {
            scrollX: true,
            eventPassthrough: 'vertical'
          });
        } else {
          this.scroll.refresh();
        }
      });
    }
  },
  components: {
    star,
      split
  }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .seller
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .overview
      position: relative
      padding: 18px
      .sellname
        line-height: 14px
        font-size: 14px
        color: rgb(7, 17, 27)
        margin-bottom: 8px
      .star-wrapper
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        font-size: 0
        .star
          display: inline-block
          vertical-align: top
          margin-right: 8px
        .ratingcount
          margin-right: 12px
        .ratingcount,.sellcount
          line-height: 18px
          display: inline-block
          vertical-align: top
          font-size: 10px
          color: rgb(77, 85, 93)
      .collected
        position: absolute
        top: 18px
        right: 11px
        width: 50px
        text-align: center
        .icon-favorite
          display: block
          line-height: 24px
          font-size: 24px
          margin-bottom: 4px
          color: #d4d6d9
          &.active
            color: rgb(240, 20, 20)
        .text
          line-height: 10px
          font-size: 10px
          color: rgb(77, 85, 93)
      .remark
        display: flex
        padding-top: 18px
        .block
          flex: 1
          text-align: center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border: none
          h1
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
            margin-bottom: 4px
          .content
            line-height: 24px
            font-size: 10px
            color: rgb(7, 17, 27)
            .streng
              font-size: 24px
    .bulletin
      padding: 18px 18px 0 18px
      h1
       line-height: 14px
       font-size: 14px
       color: rgb(7, 17, 27)
       margin-bottom: 8px
      .content-wrapper
        padding: 0 12px 16px 12px
        border-1px(rgba(7, 17, 27, 0.1))
        .content
          line-height: 24px
          font-size: 12px
          color: rgb(240, 20, 20)
    .supports
      padding: 0 18px
      .support-item
        padding: 16px 12px
        font-size: 0
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
      .icon
        display: inline-block
        vertical-align: top
        width: 16px
        height: 16px
        margin-right: 4px
        background-size: 16px 16px
        background-repeat: no-repeat
        &.decrease
          bg-image('decrease_4')
        &.discount
          bg-image('discount_4')
        &.guarantee
          bg-image('guarantee_4')
        &.invoice
          bg-image('invoice_4')
        &.special
          bg-image('special_4')
      .text
        line-height: 16px
        font-size: 12px
        color: rgb(7, 17, 27)
    .pics
      padding: 18px
      h1
       line-height: 14px
       font-size: 14px
       color: rgb(7, 17, 27)
       margin-bottom: 12px
      .pic-wrapper
        width: 100%
        overflow: hidden
        white-space: nowrap
        .pic-list
          font-size: 0
          .pic-item
            display: inline-block
            margin-right: 6px
            width: 120px
            height: 90px
            &:last-child
              margin: 0
    .info
      padding: 18px 18px 0 18px
      h1
       line-height: 14px
       font-size: 14px
       color: rgb(7, 17, 27)
       margin-bottom: 12px
      .info-item
        padding: 16px 12px
        line-height: 16px
        font-size: 12px
        color: rgb(7, 17, 27)
        border-top: 1px solid rgba(7, 17, 27, 0.1)
</style>
