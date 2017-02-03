<template>
  <div class="cartcontrol">
    <div class="cart-decrease icon-remove_circle_outline" v-show="food.count>0" @click.stop="decreaseCount($event)"
         transition="move">
      <span class="inner icon-remove_circle_outline"></span>
    </div>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop="addCount($event)"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';
  export default{
    props: {
      food: {
        type: Object
      }
    },
    created() {
    },
    methods: {
      addCount (event) {
        if (!event._constructed) return;
        if (!this.food.count) {
          // 添加原来不存在的属性需要这样添加变化才能被监测到
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$dispatch('cart.add', event.target);
      },
      decreaseCount(event) {
        if (!event._constructed) return;
        if (this.food.count > 0) {
          this.food.count--;
        }
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartcontrol
    font-size: 0
    .cart-decrease
      display: inline-block
      padding: 6px
      transition: all 0.4s linear
      &.move-transition
        opacity: 1
        transform: translate3D(0, 0, 0)
      &.move-enter, &.move-leave
        opacity: 0
        transform: translate3D(24px, 0, 0)
        .inner
          transform: rotate(180deg)
      .inner
        display: inline-block
        font-size: 24px
        line-height: 24px
        color: rgb(0, 160, 220)
        transition: all 0.4s linear
        transform: rotate(0)
    .cart-add
      display: inline-block
      padding: 6px
      font-size: 24px
      line-height: 24px
      color: rgb(0, 160, 220)
    .cart-count
      display: inline-block
      vertical-align: top
      width: 12px
      padding-top: 6px
      font-size: 12px
      line-height: 24px
      text-align: center
      color: rgb(147, 153, 159)
</style>
