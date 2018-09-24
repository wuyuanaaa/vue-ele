<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menu">
      <ul>
        <li
          v-for="(item, index) in goods"
          :key="index" class="menu-item"
          :class="{'current':currentIndex === index}"
          @click="selectMenu(index)">
          <span class="text">
            <span v-show="item.type > 0" class="icon-box"><icon :size="3" :type="item.type"></icon></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foods">
      <ul>
        <li v-for="(item, index) in goods" :key="index" class="food-list food-list-hook">
          <h3 class="title">{{item.name}}</h3>
          <ul>
            <li v-for="(food, index2) in item.foods" :key="index2" class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon" width="57" height="57" alt="">
              </div>
              <div class="content">
                <h4 class="name">{{food.name}}</h4>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food" @cart-add="_drop"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart
      ref="shopcart"
      :delivery-price="seller.deliveryPrice"
      :min-price="seller.minPrice"
      :select-foods="selectFoods">
    </shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
import icon from '../../components/icon/icon';
import BScroll from 'better-scroll';
import shopcart from '../../components/shopcart/shopcart';
import cartcontrol from '../../components/cartcontrol/cartcontrol';
const ERR_OK = 0;
export default {
  name: 'goods',
  props: {
    seller: Object
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    };
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
  created () {
    this.$http.get('/api/goods').then(response => {
      if (response.body.errno === ERR_OK) {
        this.goods = response.body.data;
        this.$nextTick(() => {
          this._initScroll();
          this._calculateHeight();
        });
      }
    });
  },
  methods: {
      _drop (target) {
        // 优化，异步执行下落动画
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        });
      },
      selectMenu (index) {
        let foodList = this.$refs.foods.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.foodsScroll.scrollToElement(el, 300);
      },
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menu, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foods, {
          probeType: 3,
          click: true
        });
        this.foodsScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _calculateHeight () {
        let foodList = this.$refs.foods.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
            let item = foodList[i];
            height += item.clientHeight;
            this.listHeight.push(height);
        }
      }
  },
  components: {
    icon,
    shopcart,
    cartcontrol
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .goods
    display flex
    position absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display table
        margin 0 auto
        padding 0 12px
        height 54px
        width: 56px
        line-height: 14px
        &.current
          position relative
          margin-top: -1px
          z-index 10
          background: #fff;
          font-weight 700
          .text
            border-none()
        .text
          display table-cell
          vertical-align middle
          border-bottom-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
          .icon-box
            display inline-block
            margin-right: 2px
            width: 12px
            height: 12px
            vertical-align top
        &:last-child
          .text
            border-none()
    .foods-wrapper
      flex 1
      .title
        padding-left: 14px
        height: 26px
        line-height: 26px
        border-left:2px solid #d9dde1
        font-size: 12px
        color: rgb(147,153,159)
        background-color: #f3f5f7
      .food-item
        display flex
        margin 18px
        padding-bottom: 18px
        border-bottom-1px(rgba(7, 17, 27, 0.1))
        &:nth-last-child(1)
          border-none()
          margin-bottom: 0
        .icon
          flex 0 0 57px
          margin-right: 10px
        .content
          flex 1
          .name
            margin 2px 0 8px 0
            height: 14px
            line-height: 14px
            font-size: 14px
            color: rgb(7,17,27)
          .desc,.extra
            line-height: 10px
            font-size: 10px
            color rgb(147,153,159)
          .desc
            margin-bottom 8px
            line-height: 14px
          .extra
            .count
              margin-right: 12px
          .price
            font-weight: 700
            line-height: 24px
            font-size: 0
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240,20,20)
            .old
              text-decoration line-through
              font-size: 10px
              color rgb(147,153,159)
          .cartcontrol-wrapper
            position absolute
            right: 0
            bottom 12px
</style>
