<template>
  <div class="selltitle">
    <span class="line"></span>
    <span class="titlecontent">{{titles}}</span>
    <span class="line"></span>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      titles: {
        type: String
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .selltitle
    font-size: 0
    display: flex
    width: 80%
    margin: 0 auto
    .line
      flex: 1
      position: relative
      top: -6px
      border-bottom: 1px solid rgba(255, 255, 255, 0.2)
    .titlecontent
      margin: 0 12px
      font-size: 14px
</style>
