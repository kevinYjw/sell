<template>
  <div class="ratings" ref="ratings">
    <div class="ratings-content">
      <div class="overview">
        <div class="overview-left">
          <h1 class="score">{{seller.score}}</h1>
          <div class="title">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="overview-right">
          <div class="score-wrapper">
            <span class="title">服务态度</span>
            <star :size="36" :score="seller.serviceScore" class="star"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-wrapper">
            <span class="title">商品评分</span>
            <star :size="36" :score="seller.foodScore" class="star"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="delivery-wrapper">
            <span class="title">送达时间</span>
            <span class="delivery">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <rating-select 
      :selectType="selectType" 
      :onlyContent="onlyContent"
      :ratings="ratings"
      @select="selectRating"
      @toggle="toggleContent"></rating-select>
      <div class="ratings-wrapper">
        <ul>
          <li 
          v-for="rating in ratings" 
          class="rating-item" 
          v-show="needShow(rating.rateType,rating.text)">
            <div class="avatar">
              <img :src="rating.avatar" width="28" height="28">
            </div>
            <div class="content">
              <h1 class="name">{{rating.username}}</h1>
              <div class="star-wrapper">
                <star :size="24" :score="rating.score" class="star"></star>
                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                <span class="icon-keyboard_arrow_right"></span>
                <span v-for="item in rating.recommend" class="item">{{item}}</span>
              </div>
              <div class="time">{{rating.rateTime | formatDate}}</div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import star from 'components/star/star'
import split from 'components/split/split'
import ratingSelect from 'components/ratingselect/ratingselect'
import timestamp from 'time-stamp'
import BScroll from 'better-scroll'

const POSITIVE = 0 //好评价
const NEGATIVE = 1 //不好评价
const ALL = 2 //全部评价
const ERR_OK = 0

export default {
  name: 'ratings',
  props:{
    seller:{
      type: Object
    }
  },
  components:{
    star,
    split,
    ratingSelect
  },
  data(){
    return {
      ratings: [],
      selectType: ALL,
      onlyContent: true
    }
  },
  methods:{
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
      return timestamp('YYYY-MM-DD HH:mm',date)
    }
  },
  created(){

    this.$http.get('/api/ratings').then((response) => {
      response = response.body
      if(response.errno === ERR_OK){
        this.ratings = response.data
        this.$nextTick(() => {
          this.scroll = new BScroll(this.$refs.ratings,{
            click:true
          })
        })
      }
    })
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

  .ratings
    position:absolute
    top:3.72rem
    bottom:0
    left:0
    width:100%
    overflow:hidden
    .ratings-content
      .overview
        display:flex
        padding:.36rem 0
        .overview-left
          padding-bottom:.12rem
          flex:0 0 2.75rem
          width:.2.75rem
          border-right:.02rem solid rgba(7,17,27,0.1)
          text-align:center
          @media only screen and (max-width: 320px)
            flex:0 0 2.4rem
            width:2.4rem
          .score
            margin-bottom:.12rem
            line-height:.56rem
            font-size:.48rem
            color:rgb(255,154,0)
          .title
            margin-bottom:.16rem
            line-height:.24rem
            font-size:.24rem
            color:rgb(7,17,27)
          .rank
            line-height:.2rem
            font-size:.2rem
            color:rgb(147,153,159)
        .overview-right
          flex:1
          padding:.12rem 0 .12rem .48rem
          @media only screen and (max-width: 320px)
            padding-left: 6px
          .score-wrapper
            margin-bottom:.16rem
            font-size:0
            .title
              display:inline-block
              line-height:.36rem
              vertical-align:top
              font-size:.24rem
              color:rgb(7,17,27)
            .star
              display:inline-block
              vertical-align:top
              margin:0 .24rem
            .score
              display:inline-block
              line-height:.36rem
              vertical-align:top
              font-size:.24rem
              color:rgb(255,153,0)
          .delivery-wrapper
            font-size:0
            .title
              line-height:.36rem
              font-size:.24rem
              color:rgb(7,17,27)
              margin-right:.24rem
            .delivery
              font-size:.24rem
              color:rgb(147,153,159)
      .ratings-wrapper
        padding:0 .36rem
        .rating-item
          display:flex
          padding:.36rem 0
          border-1px(rgba(7,17,27,0.1))
          .avatar
            flex: 0 0 .56rem
            width:.56rem
            margin-right:.24rem
            img
              border-radius:50%
          .content
            flex:1
            position:relative
            .name
              line-height:.24rem
              font-size:.2rem
              color:rgb(7,17,27)
              margin-bottom:.08rem
            .star-wrapper
              margin-bottom:.12rem
              font-size:0
              .star
                display:inline-block
                vertical-align:top
                margin-right:.12rem
              .delivery
                display:inline-block
                vertical-align:top
                line-height:.24rem
                font-size:.2rem
                color:rgb(147,153,159)
            .text
              margin-bottom:.16rem
              line-height:.36rem
              color:rgb(7,17,27)
              font-size:.24rem
            .recommend
              line-height:.32rem
              font-size:0
              .icon-keyboard_arrow_right,.item
                display:inline-block
                font-size:.18rem
                margin: 0 .16rem .08rem 0
              .icon-thumb_up
                color: rgb(0, 160, 220)
              .item
                padding: 0 .12rem
                border: .02rem solid rgba(7, 17, 27, 0.1)
                border-radius: .02rem
                color: rgb(147, 153, 159)
                background: #fff
            .time
              position: absolute
              top: 0
              right: 0
              line-height: .24rem
              font-size: .2rem
              color: rgb(147, 153, 159)
</style>
