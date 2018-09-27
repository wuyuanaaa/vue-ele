<template>
  <div class="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h3 class="score">{{seller.score}}</h3>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star class="star" :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect
        :select-type="selectType"
        :only-content="onlyContent"
        :desc="desc"
        :ratings="ratings"
        @changeType="changeType"
        @changeOnlyContent="changeOnlyContent"
      ></ratingselect>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../../components/star/star';
  import split from '../../components/split/split';
  import ratingselect from '../../components/ratingselect/ratingselect';
  const ALL = 2;
  const ERR_OK = 0;
  export default {
    name: 'ratings',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        ratings: [],
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      };
    },
    components: {
      star,
      split,
      ratingselect
    },
    methods: {
      changeType (type) {
        this.selectType = type;
      },
      changeOnlyContent () {
        this.onlyContent = !this.onlyContent;
      }
    },
    created () {
      this.$http.get('/api/ratings').then(response => {
        if (response.body.errno === ERR_OK) {
          this.ratings = response.body.data;
        }
      });
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .ratings
    position absolute
    top: 174px
    bottom: 0
    left: 0
    width: 100%
    overflow hidden
    .overview
      display flex
      padding 18px 0
      .overview-left
        flex 0 0 137px
        width: 137px
        border-right 1px solid rgba(7, 17, 27, 0.2)
        text-align: center
        @media only screen and (max-width: 320px)
          flex 0 0 120px
          width: 120px
        .score
          margin-bottom: 6px
          font-size: 24px
          line-height 28px
          color: rgb(255, 153, 0)
        .title
          margin-bottom: 8px
          font-size: 12px
          line-height: 12px
          color: rgb(7, 17, 27)
        .rank
          padding-bottom: 6px
          line-height: 10px
          font-size: 10px
          color: rgb(147, 153, 159)
      .overview-right
        flex 1
        padding-left: 24px
        @media only screen and (max-width: 320px)
          padding-left: 6px
        .score-wrapper
          margin-bottom: 8px
          font-size: 0
          &:last-child
            margin-bottom: 0
          .title
            display inline-block
            vertical-align top
            margin-right: 12px
            line-height: 18px
            font-size: 12px
            color: rgb(7, 17, 27)
          .star
            margin-right: 12px
            display inline-block
            vertical-align top
          .score
            display inline-block
            vertical-align top
            line-height: 18px
            font-size: 12px
            color: rgb(255, 153, 0)
          .time
            display inline-block
            vertical-align top
            line-height: 18px
            font-size: 12px
            color: rgb(147, 153, 159)
</style>
