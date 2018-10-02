<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h3 class="title">{{seller.name}}</h3>
        <div class="desc border-1px">
          <star class="star" :size="36" :score="seller.score"></star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block border-left-1px">
            <h4 class="block-title">起送价</h4>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block border-left-1px">
            <h4 class="block-title">商家配送</h4>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block border-left-1px">
            <h4 class="block-title">平均配送时间</h4>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span
            class="icon-favorite"
            :class="{'active': favorite}"
          ></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h3 class="title">公告与活动</h3>
        <div class="content-wrapper">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul class="supports">
          <li
            class="supports-list border-1px"
            v-for="(support, index) in seller.supports"
            :key="index"
          >
            <span class="icon-box"><icon :size="3" :type="support.type"></icon></span>
            <span class="des">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h3 class="title">商家实景</h3>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li
              class="pic-item"
              v-for="(pic, index) in seller.pics"
              :key="index"
            >
              <img :src="pic" width="120" height="90" alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="infos">
        <h3 class="title">商家信息</h3>
        <ul>
          <li
            class="info-item border-1px"
            v-for="(info, index) in seller.infos"
            :key="index"
          >
            {{info}}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../../components/star/star';
  import split from '../../components/split/split';
  import icon from '../../components/icon/icon';
  import BScroll from 'better-scroll';
export default {
  name: 'seller',
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: false
    };
  },
  computed: {
    favoriteText () {
      return this.favorite ? '已收藏' : '收藏';
    }
  },
  methods: {
    toggleFavorite () {
      this.favorite = !this.favorite;
    }
  },
  components: {
    star,
    split,
    icon
  },
  mounted () {
    this.scroll = new BScroll(this.$refs.seller, {
      click: true
    });
    if (this.seller.pics) {
      let picWidth = 120;
      let margin = 6;
      let width = (picWidth + margin) * this.seller.pics.length - margin;
      this.$refs.picList.style.width = width + 'px';
      this.$nextTick(() => {
        this.picScroll = new BScroll(this.$refs.picWrapper, {
          scrollX: true,
          eventPassthrough: 'vertical'
        });
      });
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .seller
    position absolute
    top 174px
    bottom: 0
    left 0
    width 100%
    overflow hidden
    .overview
      position relative
      padding 18px
      .title
        margin-bottom: 8px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .desc
        padding-bottom: 18px
        border-bottom-1px(rgba(7, 17, 27, 0.1))
        font-size: 0
        .star
          display inline-block
          vertical-align top
          margin-right: 8px
        .text
          margin-right: 12px
          display inline-block
          vertical-align top
          line-height: 18px
          font-size: 10px
          color rgb(77, 85, 93)
      .remark
        padding 18px 0 0
        display flex
        .block
          flex 1
          &+.block
            border-left-1px(rgba(7, 17, 27, 0.1))
          .block-title
            margin-bottom: 4px
            line-height: 10px
            font-size: 10px
            text-align: center
            color: rgb(147, 153, 159)
          .content
            line-height: 24px
            text-align: center
            font-size: 10px
            color: rgb(7, 17, 27)
            .stress
              font-size: 24px
      .favorite
        position: absolute
        top: 18px
        right: 18px
        width: 40px
        text-align: center
        .icon-favorite
          display block
          margin-bottom: 4px
          line-height: 24px
          font-size: 24px
          color: #d4d6d9
          &.active
            color: rgb(240, 20, 20)
        .text
          line-height: 10px
          font-size: 10px
          color: rgb(77, 85, 93)
    .bulletin
      padding 18px 18px 0
      .title
        margin-bottom: 8px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .content-wrapper
        padding 0 12px 16px
        .content
          line-height: 24px
          font-size: 12px
          color: rgb(240, 20, 20)
      .supports
        .supports-list
          padding 16px 12px
          font-size: 0
          border-top-1px(rgba(7, 17, 27, 0.1))
          .icon-box
            display inline-block
            vertical-align top
            margin-right: 6px
            height: 16px
            width: 16px
          .des
            display inline-block
            vertical-align top
            line-height: 16px
            font-size: 12px
            color: rgb(7, 17, 27)
    .pics
      padding 18px
      .title
        margin-bottom: 12px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .pic-wrapper
        width: 100%
        overflow hidden
        white-space nowrap
        .pic-list
          font-size: 0
          .pic-item
            display inline-block
            margin-right: 6px
            width: 120px
            height: 90px
            &:last-child
              margin: 0
    .infos
      padding 18px 18px 0
      .title
        margin-bottom: 12px
        font-size: 14px
        line-height: 14px
        color: rgb(7, 17, 27)
      .info-item
        padding 16px 12px
        line-height: 16px
        font-size: 12px
        color: rgb(7, 17, 27)
        border-top-1px(rgba(7, 17, 27, 0.1))
</style>
