<template>
  <transition name="move">
    <div class="food" v-show="showFlag" ref="food">
      <div class="food-content">
        <div class="image-header">
          <img :src="food.image">
          <div class="back" @click="hide">
            <i class="icon-thumb_up"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span>
            <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cart-control :food="food"></cart-control>
          </div>
          <transition name="fade">
            <div class="buy" v-show="!food.count || food.count===0" @click="addFirst($event)">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <div class="food-info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <rating-select 
          :selectType="selectType" 
          :onlyContent="onlyContent" 
          :desc="desc"
          :ratings="food.ratings"
          @select="selectRating"
          @toggle="toggleContent"></rating-select>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar" width='12' height='12' class="avatar">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <span :class="{'icon-keyboard_arrow_right':rating.rateType===0,'icon-add_circle':rating.rateType===1}"></span>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import Vue from 'vue'
import BScroll from 'better-scroll'
import cartControl from 'components/cartcontrol/cartcontrol'
import split from 'components/split/split'
import ratingSelect from 'components/ratingselect/ratingselect'
import {formatDate} from 'common/js/date'
import timestamp from 'time-stamp'

const POSITIVE = 0 //好评价
const NEGATIVE = 1 //不好评价
const ALL = 2 //全部评价

export default {
  name: 'food',
  props:{
    food:{
      type:Object
    }
  },
  components:{
    cartControl,
    split,
    ratingSelect
  },
  data(){
    return {
      showFlag: false,
      selectType: ALL,
      onlyContent: true,
      desc:{
        all: '全部',
        positive: '推荐',
        negative: '吐槽'
      }
    }
  },
  methods:{
    show(){
      this.showFlag = true
      this.selectType = ALL
      this.onlyContent = true
      this.$nextTick(() => {
        if(!this.scroll){
          this.scroll = new BScroll(this.$refs.food,{
            click:true
          })
        }
      })
    },
    hide(){
      this.showFlag = false
    },
    addFirst(event){
      if(!event._constructed){
        return
      }
      this.$emit('add', event.target)
      Vue.set(this.food,'count',1)
    },
    selectRating(type){
      this.selectType = type
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    toggleContent(){
      this.onlyContent = !this.onlyContent
      this.$nextTick(() => {
        this.scroll.refresh()
      })
    },
    needShow(type,text){
      if(this.onlyContent && !text){
        return false
      }
      if (this.selectType === ALL){
        return true
      } else {
        return type === this.selectType
      } 
    }
  },
  filters:{
    formatDate(time){
      let date = new Date(time)
      // return formatDate(date,'yyyy-MM-dd hh:mm')
      return timestamp('YYYY-MM-DD HH:mm',date)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

  .food
    position:fixed
    left:0
    top:0
    bottom:.96rem
    z-index:30
    width:100%
    background-color:#fff
    transform: translate3d(0, 0, 0)
    &.move-enter-active,&.move-leave-active
      transition:all 0.2s linear
    &.move-enter,&.move-leave-active
      transform: translate3d(100%, 0, 0)
    .food-content
      .image-header
        position:relative
        width:100%
        height:0
        padding-bottom:100%
        img
          position:absolute
          top:0
          left:0
          width:100%
          height:100%
        .back
          position:absolute
          top:.20rem
          left:0
          .icon-thumb_up
            display:block
            padding:.20rem
            font-size:.4rem
            color:#fff
      .content
        position:relative
        padding:.36rem
        .title
          line-height:.28rem
          margin-bottom:.16rem
          font-size:.28rem
          font-weight:700
          color:rgb(7,17,27)
        .detail
          margin-bottom:.36rem
          line-height:.2rem
          font-size:0
          height:.2rem
          .sell-count,.rating
            font-size:.2rem
            color:rgb(147,153,159)
          .sell-count
            margin-right:.24rem
        .price
          font-weight:700
          line-height:.48rem
          .now
            margin-right:.16rem
            font-size:.28rem
            color:rgb(240,20,20)
          .old
            text-decoration:line-through
            font-size:.2rem
            color:rgb(147,153,159)
        .cartcontrol-wrapper
          position:absolute
          right:.24rem
          bottom:.24rem
        .buy
          position:absolute
          right:.36rem
          bottom:.36rem
          z-index:10
          height:.48rem
          line-height:.48rem
          text-align:center
          padding:0 .24rem
          box-sizing:border-box
          font-size:.20rem
          border-radius:.24rem
          color:#fff
          background-color:rgb(0,160,220)
          opacity: 1
          &.fade-enter-active, &.fade-leave-active
            transition: all 0.2s
          &.fade-enter, &.fade-leave-active
            opacity: 0
            z-index: -1
      .food-info
        padding:.36rem
        .title
          line-height:.28rem
          margin-bottom:.12rem
          font-size:.28rem
          color:rgb(7,17,27)
          font-weight:400
        .text
          color:rgb(77,85,93)
          line-height:.48rem
          padding:0 .16rem
          font-size:.24rem
      .rating
        padding-top:.36rem
        .title
          line-height:.28rem
          margin-left:.36rem
          font-size:.28rem
          color:rgb(7,17,27)
        .rating-wrapper
          padding:0 .36rem
          .rating-item
            position:relative
            padding:.32rem 0
            border-1px(rgba(7,17,27,0.1))
            .user
              position:absolute
              right:0
              top:.32rem
              line-height:.24rem
              font-size:0
              .name
                display:inline-block
                vertical-align:top
                font-size:.2rem
                color:rgb(147,153,159)
                margin-right:.12rem
              .avatar
                border-radius:50%
            .time
              margin-bottom:.12rem
              line-height:.24rem
              font-size:.2rem
              color:rgb(147,153,159)
            .text
              line-height:.32rem
              font-size:.24rem
              color:rgb(7,17,27)
              .icon-keyboard_arrow_right, .icon-add_circle
                margin-right: 4px
                line-height: 16px
                font-size: 12px
              .icon-keyboard_arrow_right
                color: rgb(0, 160, 220)
              .icon-add_circle
                color: rgb(147, 153, 159)
          .no-rating
            padding: 16px 0
            font-size: 12px
            color: rgb(147, 153, 159)
</style>
