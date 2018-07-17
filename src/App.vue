<template>
  <div id="app">
    <v-header :seller='seller'></v-header>
    <v-nav></v-nav>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import vHeader from 'components/header/header'
import vNav from 'components/nav/nav'

const ERR_OK = 0

export default {
  name: 'App',
  data() {
    return {
      seller: {}
    }
  },
  components:{
  	vHeader,
  	vNav
  },
  created() {
    this.$http.get('/api/seller').then((response) => {
      response = response.body
      if(response.errno === ERR_OK){
        this.seller = response.data
      }
    })
  }
}
</script>

<style></style>
