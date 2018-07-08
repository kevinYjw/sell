<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width='64' height='64' :src="seller.avatar">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div class="support" v-if="seller.supports">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div class="support-count" v-if="seller.supports"  @click="showDetail">
        <span class="count">{{seller.supports.length}}</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper"  @click="showDetail">
      <span class="bulletin-title"></span>
      <span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-check_circle"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
      <div class="detail" v-show="detailShow">
        <div class="detail-wrapper clearfix">
          <div class="detail-main">
            <h1 class="name">{{seller.name}}</h1>
            <div class="star-wrapper">
              <star :size="48" :score="seller.score"></star>
            </div>
            <div class="title">
              <div class="line"></div>
              <div class="text">优惠信息</div>
              <div class="line"></div>
            </div>
            <ul v-if="seller.supports" class="supports">
              <li class="supports-item" v-for="(item,index) in seller.supports">
                <span class="icon" :class="classMap[seller.supports[index].type]"></span>
                <span class="text">{{seller.supports[index].description}}</span>
              </li>
            </ul>
            <div class="title">
              <div class="line"></div>
              <div class="text">商家公告</div>
              <div class="line"></div>
            </div>
            <div class="bulletin">
              <p class="content">{{seller.bulletin}}</p>
            </div>
          </div>
        </div>
        <div class="detail-close" @click="hideDetail">
          <i class="icon-shopping_cart"></i>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import star from 'components/star/star'

export default {
  name: 'header',
  data() {
    return {
      detailShow:false
    }
  },
  methods:{
    showDetail() {
      this.detailShow = true
    },
    hideDetail() {
      this.detailShow = false
    }
  },
  components:{
    star
  },
  props: {
    seller: {
      type: Object
    }
  },
  created() {
    this.classMap = ['decrease','discount','guarantee','invoice','special']
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

 .header
  position:relative
  color:#fff
  background-color:rgba(7,17,27,0.5) 
  overflow:hidden
  .content-wrapper
   position:relative
   padding:.48rem .24rem .56rem .48rem
   font-size:0
   display:flex
   .icon-keyboard_arrow_right
     margin-left: 2px
     ine-height: 24px
     font-size: 10px
   .support-count
     position:absolute
     right:.24rem
     bottom:.36rem
     padding: 0 .16rem
     line-height:.48rem
     height:.48rem
     border-radius:.28rem
     background:rgba(0,0,0,0.2)
     text-align:center
     .count
       font-size:.2rem
       vertical-align:top 
     .icon-keyboard_arrow_right
       font-size:.2rem
       line-height:.48rem
       margin-left:.06rem
   .avatar
    display:inline-block
    font-size:.28rem
    border-radius:.04rem
   .content
    display:inline-block
    font-size:.28rem
    margin-left:.32rem
    .title
     margin:.04rem 0 .16rem 0
     .brand
      display:inline-block
      width:.6rem
      height:.36rem
      bg-image('brand')
      background-size:.6rem .36rem
      vertical-align:top
     .name
       margin-left:.12rem
       font-size:.32rem
       line-height:.36rem
       font-weight:blod
    .description
      margin-bottom:.2rem
      line-height:.24rem
      font-size:.24rem
      letter-spacing:.03rem
    .support
      .icon
        display:inline-block
        width:.24rem
        height:.24rem
        margin-right:.08rem
        background-size:.24rem .24rem
        background-repeat:no-repeat
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
      .text
        font-size:.24rem
        letter-spacing:.03rem
  .bulletin-wrapper
    position:relative
    height:.56rem
    line-height:.56rem
    padding:0 .44rem 0 .24rem
    white-space:nowrap
    overflow:hidden
    text-overflow:ellipsis
    background-color:rgba(7,17,27,0.2)
    .bulletin-title
      display:inline-block
      width:.44rem
      height:.24rem
      bg-image('bulletin')
      background-size:.44rem .24rem
      background-repeat:no-repeat
      vertical-align:top
      margin-top:.16rem
    .bulletin-text
      font-size:.2rem
      margin:0 .08rem
      vertical-align:top
    .icon-check_circle
      position:absolute
      font-size:.2rem
      right:.24rem
      top:.16rem
  .background
    position:absolute
    top:0
    left:0
    width:100%
    height:100%
    z-index:-1
    filter:blur(.2rem)
  .detail
    position:fixed
    top:0
    left:0
    z-index:100
    width:100%
    height:100%
    overflow:auto
    background-color:rgba(7,17,27,0.8)
    backdrop-filter: blur(.05rem)
    &.fade-enter-active, &.fade-leave-active
      transition: all 0.5s
    &.fade-enter, &.fade-leave-active
      opacity: 0
      background: rgba(7, 17, 27, 0)
    .detail-wrapper
      min-height:100%
      width:100%
      .detail-main
        margin-top:1.28rem
        padding-bottom:1.28rem
        .name
          line-height:.32rem
          text-align:center
          font-size:.32rem
          font-weight:700
        .star-wrapper
          margin-top:.36rem
          padding:.04rem 0
          text-align:center
        .title
          display:flex
          width:80%
          margin:.6rem auto
          .line
            flex:1
            position:relative
            top:-6px
            border-bottom:.02rem solid rgba(255,255,255,0.2)
          .text
            padding:0 .24rem
            font-size:.28rem
            font-weight: 700
        .supports
          width:80%
          margin:0 auto
          .supports-item
            padding:0 .24rem
            margin-bottom:.24rem
            font-size:0
            &:last-child
              margin-bottom:0
            .icon
              display:inline-block
              width:.32rem
              height:.32rem
              vertical-align:top
              margin-right:.12rem
              background-size:.32rem
              background-repeat:no-repeat
              &.decrease
                bg-image('decrease_2')
              &.discount
                bg-image('discount_2')
              &.guarantee
                bg-image('guarantee_2')
              &.invoice
                bg-image('invoice_2')
              &.special
                bg-image('special_2')
            .text
              line-height:.32rem
              font-size:.24rem
        .bulletin
          width:80%
          margin:0 auto
          .content
            padding:0 .24rem
            line-height:.48rem
            font-size:.24rem
    .detail-close
      position:relative
      width:.64rem
      height:.64rem
      margin:-1.28rem auto  0 auto
      clear:both
      font-size:.64rem
</style>
