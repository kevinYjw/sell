<template>
  <div class="ratingselect">
    <div class="rating-type border-1px">
      <span @click="select(2,$event)" class="block positive" :class="{'active':selectType===2}">{{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0,$event)" class="block positive" :class="{'active':selectType===0}">{{desc.positive}}<span class="count">{{positive.length}}</span></span>
      <span @click="select(1,$event)" class="block negative" :class="{'active':selectType===1}">{{desc.negative}}<span class="count">{{negative.length}}</span></span>
    </div>
    <div @click="toggleContent($event)" class="switch" :class="{'on':onlyContent}">
      <span class="icon-thumb_down"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
  const POSITIVE = 0 //好评价
  const NEGATIVE = 1 //不好评价
  const ALL = 2 //全部评价

export default {
  name: 'ratingSelect',
  props:{
    ratings:{
      type: Array,
      default(){
        return []
      }
    },
    selectType:{
      type:Number,
      default:ALL
    },
    onlyContent:{ //是否查看有内容的评价
      type:Boolean,
      default:false
    },
    desc:{
      type:Object,
      default(){
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      }
    }
  },
  methods:{
    select(type,event){
      if(!event._constructed){
        return
      }
      this.$emit('select',type)
    },
    toggleContent(event){
      if(!event._constructed){
        return
      }
      this.$emit('toggle')
    }
  },
  computed:{
    positive(){
      return this.ratings.filter((rating) => {
        return rating.rateType = POSITIVE
      })
    },
    negative(){
      return this.ratings.filter((rating) => {
        return rating.rateType = NEGATIVE
      })
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

  .ratingselect
    .rating-type
      padding:.36rem 0
      margin:0 .36rem
      font-size:0
      border-1px(rgba(7,17,27,0.1))
      .block
        display:inline-block
        padding:.16rem .24rem
        margin-right:.16rem
        font-size:.24rem
        border-radius:.02rem
        color:rgb(77,85,93)
        line-height:.32rem
        &.active
          color:#fff
        &.positive
          background-color:rgba(0,160,220,0.2)
          &.active
            background-color:rgb(0,160,220)
        &.negative
          background-color:rgba(77,85,93,0.2)
          &.active
            background-color:rgb(77,85,93)
        .count
          font-size:.16rem
          margin-left:.04rem
    .switch
      padding:.24rem .36rem
      line-height:.48rem
      border-bottom:.02rem solid rgba(7,17,27,0.1)
      color:rgb(147, 153, 159)
      font-size:0
      &.on
        .icon-thumb_down
          color:#00c850
      .icon-thumb_down
        display: inline-block
        vertical-align: top
        margin-right: 4px
        font-size: 24px
      .text
        display: inline-block
        vertical-align: top
        font-size: 12px
</style>
