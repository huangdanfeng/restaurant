<template>
  <div class="header">
    <!--商家简介-->
    <div class="content-wrapper">
      <!--店内logo-->
      <div class="avatar">
        <img width="64px" height="64px" :src="seller.avatar" alt="加载失败">
      </div>
      <!--活动内容-->
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟
        </div>
        <div v-if="seller.supports" class="support">
          <span class="shopicon" :class="shopdetails[seller.supports[0].type]"></span>
          <span class="shopicon-dec">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count"  @click="showClick">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <!--店内公告-->
    <div class="bulletin-wrapper"  @click="showClick">
      <span class="bulletin"></span><span class="bull-dec">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <image width="100%" height="100%" :src="seller.avatar" alt="加载失败"></image>
    </div>
    <!--弹框显示详情-->
    <div class="detailShow" transition="expand" v-show="shows">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :score="seller.score" :size="48"></star>
          </div>
          <sell-title :titles="titleOne"></sell-title>
          <div class="shop-discount">
            <my-component v-for="support in seller.supports" :seller="support"></my-component>
          </div>
          <sell-title :titles="titleTwo"></sell-title>
          <div class="sell-banner">
            {{seller.bulletin}}
          </div>
        </div>
      </div>
      <div class="detail-close" @click="showHide">
        <span class="icon-close"></span>
      </div>
      <div class="shopDecback"></div>
    </div>
  </div>

</template>

<script type="text/ecmascript-6">
  import star from 'components/star/star';
  import selltitle from 'components/sellTitle/sellTitle';
  import selldisc from 'components/discount/discount';
  export default {
    // props绑定父组件的数据
    props: {
      seller: {
        type: Object
      },
      details: {
        type: Array
      }
    },
    data () {
      return {
        shows: false,
        titleOne: '优惠信息',
        titleTwo: '商家公告',
        shopdetails: ['decrease', 'discount', 'special', 'invoice', 'guatantee']
      };
    },
    methods: {
      showClick: function () {
        this.shows = true;
      },
      showHide: function () {
        console.log(this.shows);
        this.shows = false;
      }
    },
    components: {
      // <my-component> 将只在父模板可用
      'my-component': selldisc,
      'star': star,
      'sell-title': selltitle
    }
  };
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

  .header
    position: relative
    color: #fff
    overflow: hidden
    background: rgba(7, 17, 27, 0.5)
    .content-wrapper
      position: relative
      padding: 24px 12px 18px 24px
      font-size: 0
      .avatar
        display: inline-block
        vertical-align: top
      .content
        display: inline-block
        margin-left: 16px
        .title
          margin: 2px 0 8px 0
          .brand
            display: inline-block
            width: 30px
            height: 18px
            bg-image('brand')
            background-size: 30px 18px
            vertical-align: top
          .name
            margin: 0 0 0 6px
            font-size: 16px
            line-height: 18px
            font-weight: bold
        .description
          margin-bottom: 10px
          font-size: 12px
          line-height: 12px
        .support
          .shopicon
            display: inline-block
            margin: 0 4px 4px 0
            width: 12px
            height: 12px
            background-size: 12px 12px
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
          .shopicon-dec
            font-size: 10px
            line-height: 12px
      .support-count
        position: absolute
        right: 12px
        bottom: 14px
        padding: 0 8px
        height: 24px
        line-height: 24px
        text-align: center
        background: rgba(0, 0, 0, 0.2)
        border-radius: 14px
        .count
          vertical-align: top
          font-size: 10px
        .icon-keyboard_arrow_right
          line-height: 24px
          margin-left : 2px
          font-size: 10px
    .bulletin-wrapper
      position: relative
      height: 28px
      line-height: 28px
      background: rgba(7, 17, 27, 0.2)
      padding: 0 22px 0 12px
      white-space: nowrap
      overflow: hidden
      text-overflow: ellipsis
      .bulletin
        display: inline-block
        width: 22px
        height: 12px
        margin-top: 8px
        bg-image('bulletin')
        background-size: 22px 12px
        vertical-align: top
      .bull-dec
        margin: 0 4px
        vertical-align: top
        font-size: 10px
      .icon-keyboard_arrow_right
        position: absolute
        font-size: 10px
        right: 12px
        top: 8px
        vertical-align: top
    .background
      position: absolute
      top: 0
      left: 0
      width: 100%
      height: 100%
      z-index: -1
      filter: blur(10px)
    .detailShow
      width: 100%
      height: 100%
      position: fixed
      top: 0
      left: 0
      overflow: auto
      z-index: 100
      backdrop-filter: blur(10px)
      background: rgba(7, 17, 27, 0.8)
      &.expand-transition
        transition: all 0.4s
        opacity: 1
        background: rgba(7, 17, 27, 0.8)
      &.expand-enter, &.expand-leave
         background: rgba(7, 17, 27, 0)
         opacity: 0
      .detail-wrapper
        width: 100%
        min-height: 100%
        .detail-main
          margin-top: 64px
          padding-bottom: 64px
          .name
            text-align: center
            font-size: 16px
            font-weight: 700
            line-height: 16px
          .star-wrapper
            text-align: center
            margin: 16px 0 28px 0
          .shop-discount
            font-size: 12px
            width: 80%
            margin: 24px auto 28px auto
          .sell-banner
            width: 80%
            margin: 24px auto 0 auto
            font-size: 12px
            line-height: 24px
      .detail-close
        position: relative
        clear: both
        width: 32px
        height: 32px
        font-size: 32px
        margin: -64px auto 0 auto

</style>
