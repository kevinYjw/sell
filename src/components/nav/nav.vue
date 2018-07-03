<template>
  <div class="nav">
    <div class="nav-item">
    	<router-link to="/goods">
    		商品
    	</router-link>
    </div>
    <div class="nav-item">
    	<router-link to="/ratings">
    		评论
    	</router-link>
    </div>
    <div class="nav-item">
    	<router-link to="/seller">
    		商家
    	</router-link>
    </div>
  </div>
</template>

<script>

export default {
  name: 'nav'
}
</script>

<style lang="stylus" scoped>

  .nav
    display:flex
    width:100%
    height:.8rem
    line-height:.8rem
    border:.02rem solid rgba(7,17,27,0.1)
    .nav-item
      flex:1
      text-align:center
      & > a
        display: block
        font-size: .28rem
        color: rgb(77, 85, 93)
        &.active
          color:rgb(240, 20, 20)
</style>
