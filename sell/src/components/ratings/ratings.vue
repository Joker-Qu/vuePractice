<template>
  <div class="ratings" v-el:ratings>
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :select-type="selectType" :only-content="onlyContent"
                    :ratings="ratings"></ratingselect>
      <div class="ratings-wrapper">
        <li v-for="rating in ratings" class="rating-item" v-show="needShow(rating.rateType,rating.text)">
          <div class="avatar">
            <img :src="rating.avatar" width="28" height="28">
          </div>
          <div class="content">
            <h1 class="name">{{rating.username}}</h1>
            <div class="star-wrapper">
              <star :size="24" :score="rating.score"></star>
              <span class="deliverytime" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟</span>
            </div>
            <p class="text">{{rating.text}}</p>
            <div class="recommend" v-show="rating.recommend && rating.recommend.length>0">
              <span class="icon-thumb_up"></span>
              <span v-for="item in rating.recommend" class="item">{{item}}</span>
            </div>
            <div class="time">{{rating.rateTime | formateDate}}</div>
          </div>
        </li>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import star from 'components/star/star';
  import split from 'components/split/split';
  import ratingselect from 'components/ratingselect/ratingselect';
  import {formateDate} from 'common/js/date';
  const ALL = 2;
  const ERR_OK = 0;
  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    created() {
      this.$http.get('/api/ratings').then((response) => {
        response = response.body;
        if (response.errno === ERR_OK) {
          this.ratings = response.data;
          // dom更新后的回调
          this.$nextTick(() => {
            this.scroll = new BScroll(this.$els.ratings, {
              click: true
            });
          });
        }
      });
    },
    methods: {
      needShow(rateType, text) {
        if (this.onlyContent && !text) return false;
        if (this.selectType === ALL) {
          return true;
        } else {
          return rateType === this.selectType;
        }
      }
    },
    components: {
      star, split, ratingselect
    },
    events: {
      'ratingype.select'(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      'content.toggled'(onlyContent) {
        this.onlyContent = onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    },
    filters: {
      formateDate(time) {
        let date = new Date(time);
        return formateDate(date, 'yyyy-MM-dd hh:mm');
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    position: absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow: hidden
    .overview
      display: flex
      padding: 18px 0
      .overview-left
        flex: 0 0 137px
        width: 137px
        padding: 6px 0
        border-right: 1px solid rgba(7, 17, 27, 0.1)
        text-align: center
        @media only screen and (max-width: 320px)
          flex: 0 0 120px
          width: 12px
        .score
          line-height: 28px
          font-size: 24px
          margin-bottom: 6px
          color: rgb(255, 153, 0)
        .title
          line-height: 12px
          font-size: 12px
          margin-bottom: 8px
          color: rgb(7, 17, 27)
        .rank
          line-height: 10px
          font-size: 10px
          color: rgb(147, 153, 159)
      .overview-right
        flex: 1
        padding: 6px 0px 6px 24px
        @media only screen and (max-width: 320px)
          padding-left: 6px
        .score-wrapper
          margin-bottom: 8px
          font-size: 0px
          .title
            display: inline-block
            line-height: 18px
            vertical-align: top
            font-size: 12px
            color: rgb(7, 17, 27)
          .star
            display: inline-block
            vertical-align: top
            margin: 0 12px
          .score
            display: inline-block
            line-height: 18px
            vertical-align: top
            font-size: 12px
            color: rgb(255, 153, 0)

        .delivery-wrapper
          font-size: 0
          .title
            line-height: 18px
            font-size: 12px
            color: rgb(7, 17, 27)
          .time
            margin-left: 12px
            font-size: 12px
            color: rgb(147, 153, 159)

    .ratings-wrapper
      padding: 0 18px
      .rating-item
        display: flex
        padding: 18px 0
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          display: 0 0 28px
          width: 28px
          margin-right: 12px
          img
            border-radius: 50%
        .content
          position: relative
          flex: 1
          .name
            line-height: 12px
            margin-bottom: 4px
            font-size: 10px
            color: rgb(7, 17, 27)
          .star-wrapper
            margin-bottom: 6px
            font-size: 0
            .star
              display: inline-block
              vertical-align: top
              margin-right: 6px
            .deliverytime
              display: inline-block
              vertical-align: top
              font-size: 10px
              color: rgb(147, 153, 159)
          .text
            line-height: 18px
            font-size: 12px
            margin-bottom: 8px
            color: rgb(7, 17, 27)
          .recommend
            line-height: 16px
            font-size :0
            .icon-thumb_up, .item
              display: inline-block
              margin: 0 8px 4px 0
              font-size: 9px
            .icon-thumb_up
              color :rgb(0,160,220)
            .item
              padding :0 6px
              border :1px solid rgba(7,17,27,0.1)
              border-radius :1px
              color :rgb(147,153,159)
              background :white

          .time
            position :absolute
            top:0
            right:0
            font-size :10px
            color: rgb(147, 153, 159)
</style>
