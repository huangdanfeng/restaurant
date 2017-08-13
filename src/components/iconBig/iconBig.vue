<template>
  <span class="icon" :class="details[sellers.type]"></span>
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
        details: ['decrease1', 'discount1', 'special1', 'invoice1', 'guarantee1']
      };
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

  .icon
    display: inline-block
    margin: 0 2px 0 0
    width: 12px
    height: 12px
    background-size: 12px 12px
    vertical-align: top
    &.decrease1
      bg-image('decrease_3')
    &.discount1
      bg-image('discount_3')
    &.guarantee1
      bg-image('guarantee_3')
    &.invoice1
      bg-image('invoice_3')
    &.special1
      bg-image('special_3')
</style>
