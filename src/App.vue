<template>
  <div>
    <v-header :seller="seller" :details="details"></v-header>
    <!--商家tab页-->
    <div class="tab border-one">
      <div class="tab-item">
        <a v-link="{ path: '/goods', activeClass: 'active'}">商品</a>
      </div>
      <div class="tab-item">
        <a v-link="{ path: '/ratings', activeClass: 'active'}">评论</a>
      </div>
      <div class="tab-item">
        <a v-link="{ path: '/seller', activeClass: 'active'}">商家</a>
      </div>
    </div>
    <!--点菜内容详情-->
    <router-view></router-view>
  </div>
</template>

<script type="text/ecmascript-6">
  import header from 'components/header/header';
  const success = 0;

  export default {
    data () {
      return {
        seller: {

        },
        detailss: ['decrease', 'discount', 'special', 'invoice', 'guarantee']
      };
    },
    created () {
      this.$http.get('/api/seller').then((response) => {
          if (response.body.status === success) {
            // 获取商家详情信息
            this.$set('seller', response.body.data);
          }
        }, (response) => {

      });
    },
    components: {
      'v-header': header
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "common/stylus/mixin";
  .tab
    display: flex
    width: 100%
    height: 40px
    line-height: 40px
    border-one(after, bottom, rgba(7, 17, 27, 0.1))
    border-one(before, top, rgba(7, 17, 27, 0.1))
    .tab-item
      flex: 1
      text-align: center
      & > a
        display: block
        font-size: 14px
        color: rgb(77, 85, 93)
        &.active
          color: rgb(240, 20, 20)
</style>
