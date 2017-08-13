<template>
  <div class="cartool">
    <span class="remove" v-show="food.count>0"  @click="decrease($event)" transition="expand">
      <span class="inner icon-remove_circle_outline"></span>
    </span>
    <span class="text" v-show="food.count>0">{{food.count}}</span>
    <span class="add icon-add_circle" @click="add($event)"> </span>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default {
    props: {
      food: {

      }
    },
    created () {
    },
    methods: {
      decrease (event) {
        if (!event._constructed) {
          return;
        }
        if (this.food.count) {
          this.food.count -= 1;
        }
        this.$dispatch('cart.remove');
      },
      add (event) {
        if (!event._constructed) {
          return;
        }
        if (!this.food.count) {
          Vue.set(this.food, 'count', 0);
        }
        this.food.count++;
        this.$dispatch('cart.add', event.target);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartool
    font-size: 0
    .remove
      display: inline-block
      padding: 6px
      transition: all 0.3s linear 0s
      &.expand-transition
        opacity: 1
        transform: translate3D(0, 0, 0)
        .inner
          display: inline-block
          line-height: 24px
          font-size: 24px
          color: rgb(0, 160, 220)
          transform: rotate(0)
          transition: all 0.3s linear 0s
      &.expand-enter, &.expand-leave
        opacity: 0
        transform: translate3D(24px, 0, 0)
        .inner
          transform: rotate(180deg)
    .text
      display: inline-block
      padding: 0 6px
      width: 12px
      color: rgb(147, 153, 159)
      line-height: 24px
      vertical-align: top
      padding-top: 6px
      font-size: 10px
      text-align: center
    .add
      color: rgb(0, 160, 220)
      display: inline-block
      padding: 6px
      line-height: 24px
      font-size: 24px

</style>
