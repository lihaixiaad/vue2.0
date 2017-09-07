<template>
<div class="ratings" ref="rating">
   <div class="ratings-content">
     <div class="overview">
       <div class="overview-left">
         <h1 class="score">{{seller.score}}</h1>
         <div class="text">综合评分</div>
         <div class="ratio">高于周边商家{{seller.rankRate}}%</div>
       </div>
       <div class="overview-right">
         <div class="score-wrapper">
           <span class="text">服务态度</span>
           <star :score="seller.serviceScore" :size="36"></star>
           <span class="score">{{seller.serviceScore}}</span>
       </div>
         <div class="score-wrapper">
           <span class="text">服务态度</span>
           <star :score="seller.foodScore" :size="36" class="star"></star>
           <span class="score">{{seller.foodScore}}</span>
       </div>
         <div class="delivery-wrapper">
           <span class="text">送达时间</span>
           <span class="delivery">{{seller.deliveryTime}}分钟</span>
         </div>
     </div>
   </div>
     <split></split>
     <ratingselect :select-type="selectType" :only-content="onlyContent"
                   :ratings="ratings" @select="selectFood" @toggle="toggleFood" ></ratingselect>
     <div class="rating-wrapper">
       <ul>
         <li v-for="rating in ratings" v-show="needShow(rating.rateType,rating.text)" class="rating-item border-1px">
           <div class="avatar">
             <img :src="rating.avatar" width="28" height="28"/>
           </div>
           <div class="content">
            <h1 class="username">{{rating.username}}</h1>
             <div class="star-wrapper">
               <star :size="36" :score="rating.score"></star>
               <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
             </div>
             <div class="time">{{rating.rateTime | formatDate}}</div>
             <p class="text">{{rating.text}}</p>
             <div class="recommend" v-show="rating.recommend && rating.recommend.length">
               <span class="icon-thumb_up"></span>
               <span v-for="item in rating.recommend" class="item">{{item}}</span>
             </div>
           </div>
         </li>
       </ul>
     </div>
</div>
  </div>
</template>

<script type="text/ecmascript-6">
  import split from 'components/split/split';
  import star from 'components/star/star';
  import BScroll from 'better-scroll';
  import {formatDate} from 'common/js/date';
  import ratingselect from 'components/ratingselect/ratingselect';
  const ALL = 2;
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      ratings: [],
      onlyContent: false,
      selectType: ALL
    };
  },
  filters: {
    formatDate (time) {
      let date = new Date(time);
      return formatDate(date, 'yyyy-MM-dd hh:mm');
    }
  },
  created () {
    this.$http.get('/api/ratings').then((response) => {
      response = response.data;
      this.ratings = response.data;
      this.$nextTick(() => {
        this._initScroll();
      });
    });
  },
  methods: {
    _initScroll () {
       this.Scroll = new BScroll(this.$refs.rating, {
        click: true
      });
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
  components: {
    split,
    star,
    ratingselect
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'
  .ratings
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    overflow: hidden
    width: 100%
    .ratings-content
      .overview
        display: flex
        padding: 18px 0
        .overview-left
          flex: 0 0 137px
          width: 137px
          padding: 6px 0
          text-align: center
          border-right: 1px solid rgba(7, 17, 27, 0.1)
          @media only screen and (max-width: 320px)
            flex: 0 0 120px
            width: 120px
          .score
            font-size: 24px
            line-height: 28px
            color: rgb(255, 153, 0)
            margin-bottom: 6px
          .text
            font-size: 12px
            line-height: 12px
            color: rgb(7, 17, 27)
          .ratio
            font-size: 10px
            line-height: 10px
            color: rgb(147, 153, 159)
            margin-top: 8px
        .overview-right
          flex: 1
          padding: 6px 0 6px 24px
          @media only screen and (max-width: 320px)
            padding-left: 6px
          .score-wrapper
            font-size: 0
            margin-bottom: 8px
            .text
              display: inline-block
              vertical-align: top
              line-height: 18px
              font-size: 12px
              color: rgb(7, 17, 27)
            .star
              display: inline-block
              vertical-align: top
              margin: 0 12px
            .score
              display: inline-block
              vertical-align: top
              font-size: 12px
              line-height: 18px
              color: rgb(255, 153, 0)
          .delivery-wrapper
            font-size: 0
            .text
              font-size: 12px
              line-height: 18px
              color: rgb(7, 17, 27)
              margin-right: 12px
            .delivery
              font-size: 12px
              line-height: 18px
              color: rgb(147, 153, 159)
      .rating-wrapper
        padding: 0 18px
        .rating-item
          display: flex
          padding: 18px 0
          border-1px(rgba(7, 17, 27, 0.1))
          .avatar
            flex: 0 0 28px
            width: 28px
            margin-right: 12px
            img
              border-radius: 50%
          .content
            flex: 1
            position: relative
            .username
              line-height: 12px
              font-size: 10px
              color: rgb(7, 17, 27)
            .star-wrapper
              margin: 4px 0 6px 0
              font-size: 0
              .star
                display: inline-block
                vertical-align: top
                margin-right: 6px
              .delivery
                display: inline-block
                vertical-align: top
                line-height: 12px
                font-size: 10px
                color: rgb(147, 153, 159)
            .text
              line-height: 18px
              font-size: 12px
              color: rgb(7, 17, 27)
              margin-bottom: 8px
            .recommend
              line-height: 16px
              font-size: 0
              .icon-thumb_up
                font-size: 12px
                color: rgb(0, 160, 220)
                margin-right: 8px
              .item
                font-size: 9px
                color: rgb(147, 153, 159)
                padding: 0 6px
                border: 1px solid rgba(7, 17, 27, 0.1)
                border-radius: 2px
            .time
              position: absolute
              right: 0
              top: 0
              font-size: 10px
              line-height: 12px
              color: rgb(147, 153, 159)







</style>
