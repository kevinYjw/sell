<template>
  <div class="cartcontrol">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count>0" @click="decreaseCart"><i class="icon-close"></i></div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add" @click="addCart"><i class="icon-arrow_lift"></i></div>
  </div>
</template>

<script>
import Vue from 'vue'

export default {
  name: 'cartControl',
  props:{
    food:{
      type:Object
    }
  },
  methods:{
    addCart(event){
      if(!event._constructed){
        return
      }
      if(!this.food.count){
        Vue.set(this.food,'count',1)
      } else {
        this.food.count ++
      }
      this.$emit('add',event.target)
    },
    decreaseCart(event){
      if(!event._constructed){
        return
      }
      if(this.food.count){
        this.food.count --
      }
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

  .cartcontrol
    font-size:0
    .cart-decrease
      display:inline-block
      padding:.12rem
      font-size:.48rem
      line-height:.48rem
      color:rgb(0,160,220)
      transform: translate3d(0, 0, 0)
      &.move-enter-active, &.move-leave-active
        transition: all 0.4s linear
      &.move-enter, &.move-leave-active
        opacity: 0
        transform: translate3d(24px, 0, 0)
    .cart-count
      display:inline-block
      vertical-align:top
      width:.24rem
      padding-top:.12rem
      line-height:.48rem
      text-align:center
      font-size:.20rem
      color:rgb(147,153,159)
    .cart-add
      display:inline-block
      padding:.12rem
      font-size:.48rem
      line-height:.48rem
      color:rgb(0,160,220)
</style>
