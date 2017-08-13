<template>
  <div class="shopcart">
    <div class="shopprice">
      <div @click="shopdetail" class="icon">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight': totalCount > 0}">
            <span class="icon-shopping_cart"></span>
          </div>
          <div class="num" v-show="totalCount>0">
            <span>{{totalCount}}</span>
          </div>
        </div>
      </div>
      <div class="total-wrapper">
        <div>
          <span class="total" :class="{'highlight': total > 0}">¥{{total}}</span>
          <span class="deliveryprice">另需配送费¥{{seller.deliveryPrice}}元</span>
        </div>
      </div>
    </div>
    <div class="totalprice">
      <div class="price" :class="{'payready': pay}">
        {{paytotal}}
      </div>
    </div>
    <div class="ball-wrapper">
      <div transition="drop" class="ball" v-show="ball.show" v-for="ball in balls">
        <div class="inner inner-hook"></div>
      </div>
    </div>
    <div v-show="cartdetail" class="shopDetail">
      <div class="detailtotal"  transition="cartshow">
        <div class="title">
          <span class="cart">购物车</span>
          <span class="remove" @click="remove">清空</span>
        </div>
        <div class="details"  v-el:cart-box>
          <div>
            <div class="content border-one" v-for="selectfood in selectfoods">
              <span class="text">{{selectfood.name}}</span>
              <span class="price">¥{{selectfood.price * selectfood.count}}</span>
              <span class="babel">
                <v-carttool :food="selectfood"></v-carttool>
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<script type="text/ecmascript-6">
  import carttool from 'components/carttool/carttool';
  import BScroll from 'better-scroll';

  const success = 0;

  export default {
    props: {
      selectfoods: {
        type: Array,
        default () {
          return [
            {
              price: 0,
              count: 0
            }
          ];
      }
      }
    },
    data () {
      return {
        seller: {
        },
        pay: false,
        isInit: false,
        dropBalls: [],
        fold: true,
        balls: [
          {show: false},
          {show: false},
          {show: false},
          {show: false},
          {show: false}
        ]
      };
    },
    computed: {
      paytotal () {
        if (this.total === 0) {
          this.pay = false;
          return `¥${this.seller.minPrice}起送`;
        } else if (this.total < this.seller.minPrice) {
          this.pay = false;
          return `还差¥${this.seller.minPrice - this.total}起送`;
        } else {
          this.pay = true;
          return '去结算';
        }
      },
      total () {
        let total = 0;
        this.selectfoods.forEach((items) => {
          total += items.price * items.count;
        });
        return total;
      },
      totalCount () {
        let totalCount = 0;
        this.selectfoods.forEach((items) => {
          totalCount += items.count;
        });
        return totalCount;
      },
      cartdetail () {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.cartScroll) {
              this.initScroll();
            } else {
              this.cartScroll.refresh();
            }
          });
        }
        return show;
      }
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
    methods: {
      drop (el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      shopdetail () {
        if (this.totalCount > 0) {
          this.fold = !this.fold;
        }
      },
      remove () {
        this.cartdetail = !this.cartdetail;
        for (let i = 0; i < this.selectfoods.length; i++) {
          this.selectfoods[i].count = 0;
        }
      },
      decrease () {
        let show = 0;
        for (let i = 0; i < this.selectfoods.length; i++) {
          if (this.selectfoods[i].count !== 0) {
            show++;
          }
        }
        if (show === 0 && this.cartdetail === true) {
          this.cartdetail = false;
        }
      },
      initScroll () {
        this.cartScroll = new BScroll(this.$els.cartBox, {
          // better-scroll 会将点击事件去掉，要在这里开启，同时点击在PC 会被执行两次，要在这里控制
          click: true
        });
      }
    },
    transitions: {
      drop: {
        beforeEnter (el) {
          /* eslint-disable no-unused-vars */
          let count = this.balls.length;
          while (count--) {
            let ball = this.balls[count];
            if (ball.show) {
              let rect = ball.el.getBoundingClientRect();
              let x = rect.left - 32;
              let y = -(window.innerHeight - rect.top - 22);
              el.style.display = '';
              el.style.transform = `translate3d(0, ${y}px, 0)`;
              el.style.webkitTransform = `translate3d(0, ${y}px, 0)`;
              let inner = el.getElementsByClassName('inner-hook')[0];
              inner.style.transform = `translate3d(${x}px, 0, 0)`;
              inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
            }
          }
        },
        enter (el) {
          let rf = el.offsetHeight;
          this.$nextTick(() => {
            el.style.transform = 'translate3d(0, 0, 0)';
            el.style.webkitTransform = 'translate3d(0, 0, 0)';
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.transform = 'translate3d(0, 0, 0)';
            inner.style.webkitTransform = 'translate3d(0, 0, 0)';
          });
        },
        afterEnter (el) {
          let ball = this.dropBalls.shift();
          if (ball) {
            ball.show = false;
            el.style.display = 'none';
          }
        }
      }
    },
    components: {
      'v-carttool': carttool
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin";

  .shopcart
    display: flex
    width: 100%
    position: fixed
    left: 0
    bottom: 0
    font-size: 0
    height: 52px
    .shopprice
      display: flex
      flex: 1
      background: #141d27
      .icon
        flex: 0 0 80px
        height: 100%
        color: rgba(255, 255, 255, 0.4)
        .logo-wrapper
          position: relative
          top: -12px
          margin: 0 12px
          text-align: center
          width: 58px
          height: 58px
          background: #141d27
          border-radius: 50%
          .logo
            position: relative
            top: 7px
            margin: 0 7px
            text-align: center
            width: 44px
            height: 44px
            line-height: 44px
            font-size:24px
            background: rgba(255, 255, 255, 0.2)
            border-radius: 50%
            &.highlight
              background: rgb(0, 160, 220)
              color: #fff
          .num
            position: absolute
            top: 0
            right: 0
            width: 24px
            height: 16px
            line-height: 16px
            font-size: 10px
            font-weight: 700
            background: rgb(240, 20, 20)
            border-radius: 12px
            box-shadow: 0 4px 8px 0 rgba(0,0, 0, 0.4)
            color: #fff
      .total-wrapper
        flex: 1
        .total
          display: inline-block
          text-align: center
          vertical-align: middle
          font-size: 16px
          margin-top: 14px
          padding: 4px 12px 4px  4px
          border-right: 1px solid rgba(255, 255, 255, 0.1)
          color: rgba(255, 255, 255, 0.4)
          font-weight: 700
          line-hieght: 24px
          &.highlight
            color: #fff
        .deliveryprice
          display: inline-block
          vertical-align: middle
          margin: 14px 0 0 12px
          font-size: 12px
          color: rgba(255, 255, 255, 0.4)
          font-weight: normal
          line-height: 24px
    .totalprice
      flex: 0 0 105px
      background: #2b333b
      .price
        text-align: center
        line-height: 52px
        height: 52px
        font-size: 14px
        color: rgba(255, 255, 255, 0.4)
        font-weight: 700
        &.payready
          background: #00b43c
          color: #fff
    .ball-wrapper
      .ball
        position: fixed
        left: 32px
        bottom: 22px
        z-index: 200
        &.drop-transition
          transition: all 0.4s cubic-bezier(0.41, -0.3, 0.79, 0.63)
         .inner
            width: 16px
            height: 16px
            border-radius: 50%
            background: rgb(0, 160, 220)
            transition: all 0.4s linear
    .shopDetail
      position: fixed
      left:0
      bottom: 0
      width: 100%
      height: 100%
      z-index: -1
      background: rgba(7, 17, 27, 0.6)
      .detailtotal
        position: absolute
        left: 0
        bottom: 0
        width: 100%
        height: 305px
        background: #fff
        &.cartshow-transition
          opacity: 1
          transition: all 0.4s linear
        &.cartshow-enter, &.cartshow-leave
          opacity: 0
        .title
          display: flex
          font-size:14px
          padding: 0 18px
          background: #f3f5f7
          border-bottom: 1px solid rgba(7, 17, 27, 0.1)
          .cart
            height: 40px
            line-height: 40px
            flex: 1
            color: rgb(7, 17, 27)
            font-weight: normal
          .remove
            flex: 0 0 40px
            height:40px
            line-height: 40px
            color: rgb(0, 160, 220)
        .details
          height: 211px
          overflow: hidden
          > div
            width: 100%
            .content
              display: flex
              padding: 8px 18px
              font-size: 0
              height: 36px
              line-height: 36px
              border-one(after, bottom, rgba(7, 17, 27, 0.1))
              .text
                flex: 1
                font-size: 14px
                color: rgb(7, 17, 27)
                font-weight: 700
              .price
                flex: 0 0 48px
                font-size: 14px
                font-weight: 700
                color:rgb(240, 20, 20)
              .babel
                flex: 0 0 100px
                .text
                  font-size: 10px
                  font-weight: 200
                  color: #93999f






</style>
