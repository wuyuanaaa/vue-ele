<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left" :class="{'highlight': totalCount > 0}">
          <div class="logo-wrapper">
            <div class="logo">
              <i class="icon-shopping_cart"></i>
            </div>
            <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
          </div>
          <div class="price">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="{'highlight': totalPrice >= minPrice}">
            {{payDesc}}
          </div>
        </div>
      </div>
      <div class="ball-container" v-for="(ball, index) in balls" :key="index">
        <transition
          @before-enter="beforeEnter"
          @enter="enter"
          @after-enter="afterEnter"
          :css="false"
        >
          <div v-show="ball.show" class="ball">
            <div class="inner inner-hook"></div>
          </div>
        </transition>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h3 class="title">购物车</h3>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="(food, index) in selectFoods" :key="index">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span>￥{{food.price * food.count}}</span>
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
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
import cartcontrol from '../../components/cartcontrol/cartcontrol';
import BScroll from 'better-scroll';
export default {
  name: 'shopcart',
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
      balls: [
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        },
        {
          show: false
        }
      ],
      dropBall: [],
      fold: true
    };
  },
  computed: {
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
      let str = '';
      if (this.totalPrice === 0) {
        str = `￥${this.minPrice}起送`;
      } else if (this.totalPrice < this.minPrice) {
        let diff = this.minPrice - this.totalPrice;
        str = `还差￥${diff}起送`;
      } else {
        str = '去结算';
      }
      return str;
    },
    listShow () {
      if (!this.totalCount) {
        return false;
      }
      return !this.fold;
    }
  },
  methods: {
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i];
        if (!ball.show) {
          ball.show = true;
          ball.el = el;
          this.dropBall.push(ball);
          return;
        }
      }
    },
    beforeEnter (el) {
      let count = this.balls.length;
      while (count--) {
        let ball = this.balls[count];
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect();
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22);
          el.style.display = '';
          el.style.webkitTransform = `translate3d(0,${y}px,0)`;
          el.style.transform = `translate3d(0,${y}px,0)`;
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
          inner.style.transform = `translate3d(${x}px,0,0)`;
        }
      }
    },
    enter (el, done) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight;
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)';
        el.style.transform = 'translate3d(0,0,0)';
        let inner = el.getElementsByClassName('inner-hook')[0];
        inner.style.webkitTransform = 'translate3d(0,0,0)';
        inner.style.transform = 'translate3d(0,0,0)';
        el.addEventListener('transitionend', done);
      });
    },
    afterEnter (el) {
      let ball = this.dropBall.shift();
      if (ball) {
        ball.show = false;
        el.style.display = 'none';
      }
    },
    toggleList () {
      if (!this.totalCount) {
        return;
      }
      this.fold = !this.fold;
      if (!this.fold) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      } else {
        this.scroll.refresh();
      }
    },
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0;
      });
    },
    hideList () {
      this.fold = true;
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return;
      }
      window.alert(`支付${this.totalPrice}元`);
      this.empty();
    }
  },
  updated () {
    if (!this.totalCount) {
      this.fold = true;
    }
  },
  components: {
    cartcontrol
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .shopcart
    position fixed
    left: 0
    bottom 0
    z-index 50
    width: 100%
    height: 48px
    .content
      display flex
      background: #141d27
      font-size: 0
      color: rgba(255, 255, 255, 0.4)
      .content-left
        flex 1
        &.highlight
          .logo-wrapper
            .logo
              background-color: rgb(0, 160, 220)
              color: #fff
              .icon-shopping_cart
                color: #fff
          .price
            color: #fff
        .logo-wrapper
          display inline-block
          position relative
          top -10px
          margin 0 12px
          padding 6px
          width: 56px
          height: 56px
          box-sizing border-box
          vertical-align top
          border-radius 50%
          background: #141d27
          .logo
            width: 100%
            height: 100%
            border-radius 50%
            text-align: center
            background: #2b343c
            .icon-shopping_cart
              font-size: 24px
              line-height: 44px
              color: #80858a
          .num
            position absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            text-align: center
            border-radius 16px
            font-size: 9px
            font-weight: 700
            color: #fff
            background-color: rgb(240, 20, 20)
            box-shadow 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display inline-block
          vertical-align top
          margin-top: 12px
          padding-right: 12px
          line-height: 24px
          box-sizing border-box
          border-right 1px solid rgba(255, 255, 255, 0.1)
          font-size: 16px
          font-weight 700
        .desc
          display inline-block
          vertical-align top
          margin 12px 0 0 12px
          line-height 24px
          font-size: 10px
      .content-right
        flex 0 0 105px
        width: 105px
        .pay
          height: 48px
          line-height: 48px
          text-align: center
          font-size: 12px
          font-weight: 700
          background: #2b333b
          &.highlight
            background: #00b43c
            color: #fff
    .ball-container
      .ball
        position fixed
        left: 32px
        bottom: 22px
        z-index 200
        transition all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
        .inner
          width: 16px
          height: 16px
          border-radius 50%
          background: rgb(0, 160, 220)
          transition all 0.4s linear
    .shopcart-list
      position absolute
      top 0
      left: 0
      width: 100%
      z-index -1
      transform translate3d(0,-100%,0)
      transition all 0.5s
      .list-header
        height: 40px
        line-height 40px
        padding  0 18px
        background-color: #f3f5f7
        border-bottom 1px solid rgba(7,17,27,0.1)
        .title
          float: left
          font-size: 14px
          color: rgb(7,17,27)
        .empty
          float: right
          font-size: 12px
          color: rgb(0,160,220)
      .list-content
        padding 0 18px
        max-height 217px
        overflow hidden
        background: #fff
        .food
          position relative
          padding 12px 0
          box-sizing border-box
          border-bottom-1px(rgba(7,17,27,0.1))
          .name
            line-height: 24px
            font-size: 14px
            color: rgb(7,17,27)
          .price
            position: absolute
            right: 90px
            bottom: 12px
            line-height: 24px
            font-size: 14px
            font-weight 700
            color: rgb(240,20,20)
          .cartcontrol-wrapper
            position absolute
            bottom: 6px
            right: 0
    .fold-enter, .fold-leave-to
      transform translate3d(0,0,0)
  .list-mask
    position fixed
    top 0
    left 0
    width: 100%
    height: 100%
    z-index 40
    opacity 1
    backdrop-filter blur(10px)
    background: rgba(7,17,27,0.6)
    transition all 0.5s
  .fade-enter, .fade-leave-to
    opacity 0
    background: rgba(7,17,27,0)
</style>
