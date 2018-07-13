<template>
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalCount>0}">
              <i class="icon-remove_circle_outline" :class="{'highlight':totalCount>0}"></i>
            </div>
            <div class="num" v-show="totalCount>0">1</div>
          </div>
          <div class="price" :class="{'highlight':totalCount>0}">￥{{totalPrice}}元</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>
      <div class="ball-container">
        <div v-for="ball in balls">
          <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop">
            <div class="ball" v-show="ball.show">
              <div class="inner inner-hook"></div>
            </div>
          </transition>
        </div>
      </div>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li v-for="food in selectFoods" class="food">
                <span class="name">{{food.name}}</span>
                <div class="price"><span>￥{{food.price*food.count}}</span></div>
                <div class="cartcontrol-wrapper">
                  <cart-control :food="food"></cart-control>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script>
import cartControl from 'components/cartcontrol/cartcontrol'
import BScroll from 'better-scroll'

export default {
  name: 'shopcart',
  components:{
    cartControl
  },
  data(){
    return{
      balls:[{
        show:false
      },{
        show:false
      },{
        show:false
      },{
        show:false
      },{
        show:false
      }],
      dropBalls: [],
      fold: true
    }
  },
  props:{
    selectFoods:{
      type:Array,
      default() {
        return [{}]
      }
    },
    deliveryPrice:{
      type:Number,
      default:0
    },
    minPrice:{
      type:Number,
      default:0
    }
  },
  computed:{
    totalPrice(){
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount(){
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc(){
      if(this.totalPrice === 0){
        return `￥${this.minPrice}元起送`
      } else if(this.totalPrice<this.minPrice){
        let diff = this.minPrice - this.totalPrice
        return `还差￥${diff}元`
      } else {
        return '去结算'
      }
    },
    payClass(){
      if(this.totalPrice<this.minPrice){
        return 'not-enough'
      } else {
        return 'enough'
      }
    },
    listShow(){
      if (!this.totalCount){
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show){
        this.$nextTick(() => {
          if(!this,scroll) {
            this.scroll = new BScroll(this.$refs.listContent,{
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
      return show
    }
  },
  methods:{
    drop(el) {
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
    beforeDrop(el) {
      let count = this.balls.length;
      while (count--) {
        let ball = this.balls[count];
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect();
          let x = rect.left - 32;
          let y = -(window.innerHeight - rect.top - 22);
          el.style.display = '';
          el.style.webkitTransform = `translate3d(0,${y}px,0)`;
          el.style.transform = `translate3d(0,${y}px,0)`;
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
          inner.style.transform = `translate3d(${x}px,0,0)`;
        }
      }
    },
    dropping(el, done) {
      let rf = el.offsetHeight;
      this.$nextTick(() => {
        el.style.webkitTransform = 'translate3d(0,0,0)';
        el.style.transform = 'translate3d(0,0,0)';
        let inner = el.getElementsByClassName('inner-hook')[0];
        inner.style.webkitTransform = 'translate3d(0,0,0)';
        inner.style.transform = 'translate3d(0,0,0)';
        el.addEventListener('transitionend', done);
      });
    },
    afterDrop(el) {
      let ball = this.dropBalls.shift();
      if (ball) {
        ball.show = false;
        el.style.display = 'none';
      }
    },
    toggleList(){
      if(!this.totalCount){
        return
      }
      this.fold = !this.fold
    },
    empty(){
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    hideList(){
      this.fold = true
    },
    pay(){
      if(this.totalPrice<this.minPrice){
        return
      }
      window.alert(`支付${this.totalPrice}元`)
    }
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

  .shopcart
    position:fixed
    bottom:0
    left:0
    z-index:50
    width:100%
    height:.96rem
    .content
      display:flex
      background-color:#141d27
      height:100%
      font-size:0
      z-index:50
      .content-left
        flex:1
        .logo-wrapper
          display:inline-block
          position:relative
          top:-.2rem
          margin:0 .32rem
          padding:.12rem
          width:1.12rem
          height:1.12rem
          box-sizing:border-box
          vertical-align:top
          border-radius:50%
          background-color:rgb(20,29,39)
          .num
            position:absolute
            top:0
            right:0
            width:.48rem
            height:.32rem
            line-height:.32rem
            text-align:center
            border-radius:.32rem
            font-size:.18rem
            font-weight:700
            color:#fff
            background-color:rgb(240,20,20)
            box-shadow:0 .08rem .16rem rgba(0,0,0,0.4)
          .logo
            width:100%
            height:100%
            border-radius:50%
            background-color:#2b343c
            text-align:center
            &.highlight
              background-color:rgb(0,160,220)
            .icon-remove_circle_outline
              font-size:.48rem
              line-height:.88rem
              color:rgba(255,255,255,0.4)
              &.highlight
                color:#fff
        .price
          display:inline-block
          vertical-align:top
          line-height:.48rem
          margin:.24rem 0
          box-sizing:border-box
          padding-right:.24rem
          border-right:.02rem solid rgba(255,255,255,0.1)
          font-size:.30rem
          color:rgba(255,255,255,0.4)
          &.highlight
            color:#fff
        .desc
          display:inline-block
          vertical-align:top
          line-height:.48rem
          margin:.24rem 0 0 .24rem
          font-size:.20rem
          color:rgba(255,255,255,0.4)
          font-weight:400
      .content-right
        flex:0 0 2.1rem
        width:2.1rem
        .pay
          font-size:.24rem
          height:.96rem
          line-height:.96rem
          font-weight:700
          color:rgba(255,255,255,0.4)
          text-align:center
          &.not-enough
            background-color:#2b333b
          &.enough
            background-color:#00b43c
            color:#fff
    .ball-container
      .ball
        position:fixed
        left:.64rem
        bottom:.44rem
        z-index:200
        transition:all 0.4s cubic-bezier(0.49,-0.29,0.75,0.41)
        .inner
          width:.32rem
          height:.32rem
          border-radius:50%
          background-color:rgb(0,160,220)
          transition:all 0.4s linear
    .shopcart-list
      position:absolute
      top:0
      left:0
      z-index:-1
      width:100%
      transform: translate3d(0, -100%, 0)
      &.fold-enter-active, &.fold-leave-active
        transition: all 0.5s
      &.fold-enter,&.fold-leave-active
        transform:translate3d(0,0,0)
      .list-header
        height:.8rem
        line-height:.8rem
        padding:0 .36rem
        background-color:#f3f5f7
        border-bottom:.02rem solid rgba(7,17,27,0.1)
        .title
          float:left
          font-size:.28rem
          color:rgb(7,17,27)
        .empty
          float:right
          font-size:.24rem
          color:rgb(0,160,220)
      .list-content
        overflow:hidden
        padding:0 .36rem
        max-height:4.34rem
        background-color:#fff
        .food
          position:relative
          padding:.24rem 0
          box-sizing:border-box
          border-1px(rgba(7,17,27,0.1))
          .name
            line-height:.48rem
            font-size:.28rem
            color:rgb(7,17,27)
          .price
            position:absolute
            right:1.8rem
            bottom:.28rem
            line-height:.48rem
            font-size:.28rem
            font-weight:700
            color:rgb(240,20,20)
          .cartcontrol-wrapper
            position:absolute
            right:0
            bottom:.12rem
  .list-mask
      position:fixed
      top:0
      left:0
      width:100%
      height:100%
      z-index:40
      background: rgba(7, 17, 27, 0.6)
      opacity: 1
      backdrop-filter: blur(10px)
      &.fade-enter-active, &.fade-leave-active
        transition: all 0.5s
      &.fade-enter, &.fade-leave-active
        opacity: 0
        background: rgba(7, 17, 27, 0)
</style>
