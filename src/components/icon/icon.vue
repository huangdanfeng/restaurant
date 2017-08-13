<template>
  <span class="icon" :class="detailss[sellers.type]"></span>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      sellers: {
        type: Object
      }
    },
    data () {
      return {
        detailss: ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      };
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

  .icon
    display: inline-block
    margin: 0 4px 4px 0
    width: 16px
    height: 16px
    background-size: 16px 16px
    vertical-align: top
    &.decrease
      bg-image('decrease_1')
    &.discount
      bg-image('discount_1')
    &.guarantee
      bg-image('guarantee_1')
    &.invoice
      bg-image('invoice_1')
    &.special
      bg-image('special_1')
</style>
