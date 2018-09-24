<template>
    <span class="icon" :class="classMap[type]+'_'+size"></span>
</template>

<script type="text/ecmascript-6">
export default {
  name: 'icon',
  props: {
    type: Number,
    size: Number
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .icon
    display inline-block
    width: 100%
    height: 100%
    background-size: cover;
    background-repeat no-repeat
    &.decrease_1
      bg-image('decrease_1')
    &.discount_1
      bg-image('discount_1')
    &.guarantee_1
      bg-image('guarantee_1')
    &.invoice_1
      bg-image('invoice_1')
    &.special_1
      bg-image('special_1')
    &.decrease_2
      bg-image('decrease_2')
    &.discount_2
      bg-image('discount_2')
    &.guarantee_2
      bg-image('guarantee_2')
    &.invoice_2
      bg-image('invoice_2')
    &.special_2
      bg-image('special_3')
    &.decrease_3
      bg-image('decrease_3')
    &.discount_3
      bg-image('discount_3')
    &.guarantee_3
      bg-image('guarantee_3')
    &.invoice_3
      bg-image('invoice_3')
    &.special_3
      bg-image('special_3')
</style>
