<template>
  <div>
    <transition name="move">
      <div v-show="showFlag" class="food" ref="food">
        <div class="food-content">
          <div class="image-header">
            <img :src="food.image">
            <div class="back" @click="hide">
              <i class="icon-arrow_lift"></i>
            </div>
          </div>
          <div class="content">
            <h3 class="title">{{food.name}}</h3>
            <div class="detail">
              <span class="sell-count">月售{{food.sellCount}}份</span>
              <span class="rating">好评率{{food.rating}}%</span>
            </div>
            <div class="price">
              <span class="now">￥{{food.price}}</span>
              <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
            </div>
            <div class="cartcontrol-wrapper">
              <cartcontrol :food="food" @cart-add="cartAdd"></cartcontrol>
            </div>
            <transition name="fade">
              <div
                class="buy"
                v-show="!food.count || food.count === 0"
                @click="buy"
              >加入购物车</div>
            </transition>
          </div>
          <split></split>
          <div class="desc" v-show="food.info">
            <h3 class="title">商品介绍</h3>
            <div class="text">{{food.info}}</div>
          </div>
          <split v-show="food.info"></split>
          <div class="rating">
            <h3 class="title">商品评价</h3>
            <ratingselect
              ref="ratingselect"
              :desc="desc"
              :ratings="food.ratings"
            ></ratingselect>
            <div class="rating-wrapper">
              <ul v-show="food.ratings && food.ratings.length">
                <li
                  v-show="needShow(rating.rateType, rating.text)"
                  v-for="(rating, index) in food.ratings"
                  :key="index"
                  class="rating-item border-1px"
                >
                  <div class="user">
                    <span class="name">{{rating.username}}</span>
                    <img class="avatar" width="12" height="12" :src="rating.avatar">
                  </div>
                  <div class="time">{{rating.rateTime}}</div>
                  <p class="text">
                    <i
                      class="thumb"
                      :class="{'icon-thumb_up': rating.rateType === 0, 'icon-thumb_down': rating.rateType === 1}"
                    ></i>
                    <span class="rating-text">{{rating.text}}</span>
                  </p>
                </li>
              </ul>
              <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
            </div>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
  import Vue from 'vue';
  import BScroll from 'better-scroll';
  import cartcontrol from '../../components/cartcontrol/cartcontrol';
  import split from '../../components/split/split';
  import ratingselect from '../../components/ratingselect/ratingselect';
  export default {
    name: 'food',
    props: {
      food: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false,
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
        this.$nextTick(() => {
          this.$refs.ratingselect.reload();
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
      buy () {
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count = 1;
        }
        this.$emit('cart-add', event.target);
      },
      cartAdd () {
        this.$emit('cart-add', event.target);
      },
      needShow (type, text) {
        return true;
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .food
    position fixed
    top: 0
    left: 0
    bottom 48px
    padding-bottom: 48px
    width: 100%
    z-index 30
    background: #fff
    transform translate3d(0,0,0)
    transition all 0.2s linear
    .food-content
      .image-header
        position relative
        width: 100%
        height: 0
        padding-bottom 100%
        img
          position absolute
          top: 0
          left: 0
          width: 100%
          height: 100%
        .back
          position absolute
          top 6px
          left: 0
          .icon-arrow_lift
            display block
            padding 8px
            font-size: 20px
            color: #fff
      .content
        position relative
        padding 18px
        background: #fff
        .title
          margin-bottom: 8px
          font-size: 14px
          font-weight 700
          color: rgb(7,17,27)
        .detail
          margin-bottom: 18px
          height: 10px
          font-size: 0
          color: rgb(147,153,159);
          line-height: 10px
          .sell-count, .rating
            font-size: 10px
          .rating
            margin-left: 12px
        .price
          line-height: 24px
          font-size: 0
          .now
            font-size: 14px
            font-weight: 700
            color: rgb(240,20,20)
          .old
            margin-left: 12px
            text-decoration line-through
            font-size: 10px
            font-weight: 700
            color: rgb(147,153,159)
        .cartcontrol-wrapper
          position absolute
          right: 12px
          bottom: 12px
        .buy
          position absolute
          display inline-block
          right: 18px
          bottom: 18px
          z-index 10
          height: 24px
          line-height: 24px
          padding 0 12px
          box-sizing border-box
          border-radius 12px
          font-size 10px
          color: #fff
          background: rgb(0,160,220)
          opacity 1
          transition all 0.2s
        .fade-enter, .fade-leave-to
          opacity 0
      .desc
        padding: 18px
        .title
          margin-bottom: 6px
          font-size: 14px
          color: rgb(7,17,27)
        .text
          padding 0 8px
          font-size: 12px
          line-height: 24px
          color: rgb(77,85,93)
      .rating
        padding-top: 18px
        .title
          padding 0 18px
          margin-bottom: 6px
          font-size: 14px
          color: rgb(7,17,27)
        .rating-wrapper
          padding 0 18px
          .rating-item
            position relative
            padding 16px 0
            border-bottom-1px(rgba(7,17,27,0.1))
            .user
              position absolute
              right: 0
              top: 16px
              font-size: 0
              .name
                margin-right: 6px
                line-height: 12px
                font-size: 10px
                color: rgb(147,153,159)
              .avatar
                display inline-block
                vertical-align top
                border-radius 50%
            .time
              margin-bottom: 6px
              font-size: 10px
              line-height: 12px
              color: rgb(147,153,159)
            .text
              font-size: 0
              .thumb
                margin-right: 4px
                font-size: 12px
                line-height: 16px
                &.icon-thumb_up
                  color: rgb(0,160,220)
                &.icon-thumb_down
                  color: rgb(147,153,159)
              .rating-text
                line-height: 16px
                font-size: 12px
                color: rgb(7,17,27)
  .move-enter, .move-leave-to
    transform translate3d(100%,0,0)
</style>
