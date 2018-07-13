<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li 
        v-for="(item,index) in goods" 
        class="menu-item" 
        :class="{'current':currentIndex===index}"
        @click="selectMenu(index,$event)">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list" ref="foodList">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li v-for="food in item.foods" class="food-item border-1px">
              <div class="icon">
                <img :src="food.icon">
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月售{{food.sellCount}}份</span>
                  <span>好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span>
                  <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cart-control @add="addFood" :food="food"></cart-control>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart 
    :selectFoods="selectFoods" 
    :deliveryPrice="seller.deliveryPrice" 
    :minPrice="seller.minPrice"
    ref="shopcart"></shopcart>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import shopcart from 'components/shopcart/shopcart'
import cartControl from 'components/cartcontrol/cartcontrol'

const ERR_OK = 0

export default {
  name: 'goods',
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0
    }
  },
  props: {
    seller:{
      type: Object
    }
  },
  components:{
    shopcart,
    cartControl
  },
  methods:{
    _initScroll(){
      this.menuScroll = new BScroll(this.$refs.menuWrapper,{
        click: true
      })
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
        click: true,
        probeType: 3
      })

      this.foodsScroll.on("scroll",(pos) => {
        this.scrollY = Math.abs(Math.round(pos.y))
      })
    },
    _calculateHeight(){
      let foodList = this.$refs.foodList
      let height = 0
      this.listHeight.push(height)
      for (let i=0;i<foodList.length;i++){
        let item = foodList[i]
        height += item.clientHeight
        this.listHeight.push(height)
      }
    },
    selectMenu(index,event){
      if(!event._constructed){
        return
      }
      let foodList = this.$refs.foodList
      let el = foodList[index]
      this.foodsScroll.scrollToElement(el,300)
    },
    addFood(target){
      this._drop(target)
    },
    _drop(target){
      // 体验优化
      this.$nextTick(() => {
        this.$refs.shopcart.drop(target);
      });
    }
  },
  computed:{
    currentIndex(){
      for(let i=0;i<this.listHeight.length;i++){
        let height1 = this.listHeight[i]
        let height2 = this.listHeight[i+1]
        if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
          return i
        }
      }
      return 0
    },
    selectFoods(){
      let foods = []
      this.goods.forEach((good) => {
        good.foods.forEach((food) => {
          if(food.count){
            foods.push(food)
          }
        })
      })
      return foods
    }
  },
  created() {
    this.classMap = ['decrease','discount','guarantee','invoice','special']

    this.$http.get('/api/goods').then((response) => {
      response = response.body
      if(response.errno === ERR_OK){
        this.goods = response.data
        this.$nextTick(() => {
          this._initScroll()
          this._calculateHeight()
        })
      }
    })
  }
}
</script>

<style lang="stylus" scoped>
@import '~common/stylus/mixin'

  .goods
    display:flex
    position:absolute
    top:3.72rem
    width:100%
    bottom:.92rem
    overflow:hidden
    .menu-wrapper
      flex:0 0 1.6rem
      width:1.6rem
      background-color:#f3f5f7
      .menu-item
        display:table
        height:1.08rem
        line-height:.28rem
        width: 1.12rem
        padding:0 .24rem
        &.current
          position:relative
          z-index:10
          margin-top:-1px
          background:#fff
          font-weight:700
          .text
            border-none
        .icon
          display:inline-block
          width:.24rem
          height:.24rem
          margin-right:.04rem
          background-size:.24rem .24rem
          background-repeat:no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.guarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display:table-cell
          width:1.12rem
          font-size:.24rem
          vertical-align:middle
          border-1px(rgba(7,17,27,0.1))
    .foods-wrapper
      flex:1
      .food-list
        .title
          padding-left:.28rem
          height:.52rem
          line-height:.52rem
          border-left:.04rem solid #d9dde1
          font-size:.24rem
          color:rgb(147,153,159)
          background-color:#f3f5f7
        .food-item
          display:flex
          margin:.36rem
          padding-bottom:.36rem
          border-1px(rgba(7,17,27,0.1))
          &:last-child
            border-none()
            margin-bottom:0
          .icon
            flex:0 0 1.14rem
            margin-right:.2rem
            img
              width:100%
          .content
            flex:1
            .name
              font-size:.28rem
              height:.28rem
              line-height:.28rem
              margin:.04rem 0 .16rem 0
              color:rgb(7,17,27)
            .desc
              margin-bottom:.16rem
              line-height:.24rem
              font-size:.2rem
              color:rgb(147,153,159)
            .extra
              line-height:.2rem
              font-size:.2rem
              color:rgb(147,153,159)
              margin-bottom:.16rem
              &.count
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
              right:0
              bottom:.24rem
</style>
