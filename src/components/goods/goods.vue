<template>
  <div class="goods">
    <div class="menu-wrapper" v-el:menu-box>
      <ul>
        <li v-for="item in goods" class="menu-item"
            :class="{'current' : currentIndex === $index}"
            @click="toFood($index, $event)">

          <span class="menu-text border-one">
            <v-iconbig v-if="item.type != -1" :sellers="item" class="menu-icon"></v-iconbig>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="good-wrapper" v-el:food-box>
      <ul>
        <li v-for="item in goods" class="foods foods-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="items in item.foods" class="foods-item border-one">
              <div class="food-icon"><image width="57px" height="57px" :src="items.icon"></image></div>
              <div class="food-content">
                <h1 class="name">{{items.name}}</h1>
                <p v-show="items.description" class="desc">{{items.description}}</p>
                <div class="extra">
                  <span class="count">月售{{items.sellCount}}份</span>
                  <span class="rating">好评率{{items.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now"> ¥{{items.price}}</span>
                  <span class="old" v-show="items.oldPrice">¥{{items.oldPrice}}</span>
                </div>
                <div class="cart-item">
                  <v-carttool :food="items"></v-carttool>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <v-shopcart v-ref:shopcart :selectfoods="selectFoods"></v-shopcart>
  </div>
</template>

<script type="text/ecmascript-6">
  import icon from 'components/iconBig/iconBig';
  import shopcart from 'components/shopcart/shopcart';
  import carttool from 'components/carttool/carttool';
  import BScroll from 'better-scroll';
  const success = 0;

  export default {
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: {
        },
        listHeight: [],
        scrollY: 0
      };
    },
    computed: {
      currentIndex () {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (height1 <= this.scrollY && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      selectFoods () {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((items) => {
            if (items.count > 0) {
              foods.push(items);
            }
          });
        });
        return foods;
      }
    },
    created () {
      this.$http.get('/api/goods').then((response) => {
        if (response.body.status === success) {
          // 获取商家详情信息
          this.$set('goods', response.body.data);
          this.$nextTick(() => {
            this._initScroll();
            this.calculate();
          });
        }
      }, (response) => {

      });
    },
    methods: {
      _initScroll () {
        this.menueBoxScroll = new BScroll(this.$els.menuBox, {
          // better-scroll 会将点击事件去掉，要在这里开启，同时点击在PC 会被执行两次，要在这里控制
          click: true
        });
        this.foodBoxScroll = new BScroll(this.$els.foodBox, {
          // better-scroll 会将点击事件去掉，要在这里开启，同时点击在PC 会被执行两次，要在这里控制
          click: true,
          probeType: 3
        });
        this.foodBoxScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      calculate () {
        let scroll = this.$els.foodBox.getElementsByClassName('foods-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < scroll.length; i++) {
          height += scroll[i].clientHeight;
          this.listHeight.push(height);
        }
      },
      toFood (index, event) {
        if (!event._constructed) {
          return;
        }
        let scroll = this.$els.foodBox.getElementsByClassName('foods-hook');
        this.foodBoxScroll.scrollToElement(scroll[index]);
      },
      _drop (target) {
        this.$refs.shopcart.drop(target);
      },
      remove () {
        this.$refs.shopcart.decrease();
      }
    },
    components: {
      'v-iconbig': icon,
      'v-shopcart': shopcart,
      'v-carttool': carttool
    },
    events: {
      'cart.add' (target) {
        this._drop(target);
      },
      'cart.remove' () {
        this.remove();
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";
  .goods
    display: flex
    position: absolute
    top: 176px
    bottom: 52px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        font-size: 12px
        height: 54px
        line-height: 14px
        padding: 0 12px
        &.current
          position: relative
          margin-top: -1px
          z-index: 10
          color: rgb(7, 17, 27)
          font-weight: 700
          background: #fff
          .menu-text
            border-none(after)
        .menu-text
          display: table-cell
          width: 56px
          border-one(after,bottom,rgba(7, 17, 27, 0.1))
          vertical-align: middle
    .good-wrapper
      flex: 1 1 auto
      .title
        padding-left: 14px
        font-size: 12px
        rgb(147, 153, 159)
        height: 26px
        line-height: 26px
        background: #f3f5f7
        border-left: 2px solid #d9dde1
      .foods-item
        display: flex
        margin: 0 18px
        padding: 18px 0
        border-one(after,bottom,rgba(7, 17, 27, 0.1))
        &:last-child
          border-none(after)
        .food-icon
          display: inline-block
          flex: 0 0 57px
          vertical-align: top
        .food-content
          flex: 1
          display: inline-block
          padding-left: 10px
          .name
            padding: 2px 0 8px 0
            height: 14px
            font-size: 14px
            line-height: 14px
            color: rgb(7, 17, 27)
          .desc
            padding-bottom: 8px
            font-size: 10px
            color: rgb(147, 153, 159)
            line-height: 10px
          .extra
            font-size: 10px
            color: rgb(147, 153, 159)
            .count
              line-height: 10px
              padding-right:1px
          .price
            position: relative
            font-weight: 700
            width: 100%
            line-height: 24px
            .now
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              font-size: 10px
              color: rgb(147, 153, 159)
              text-decoration: line-through
          .cart-item
            position: absolute
            right: 0
            bottom: 12px

</style>
