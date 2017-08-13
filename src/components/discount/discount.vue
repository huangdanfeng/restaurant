<template>
  <div class="left">
    <v-icon :sellers="seller"></v-icon>
    <span class="icon-dec">{{seller.description}}</span>
  </div>

</template>

<script type="text/ecmascript-6">
  import icon from 'components/icon/icon';

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    components: {
      'v-icon': icon
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .left
    margin: 0 0 12px 12px
</style>
