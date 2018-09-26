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
            <ratingselect></ratingselect>
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
        showFlag: true
      };
    },
    methods: {
      show () {
        this.showFlag = true;
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
      .desc, .rating
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
  .move-enter, .move-leave-to
    transform translate3d(100%,0,0)
</style>
