<template>
  <div id="app">
    <v-header></v-header>
    <v-nav></v-nav>
    <router-view></router-view>
  </div>
</template>

<script>
import vHeader from 'components/header/header'
import vNav from 'components/nav/nav'

export default {
  name: 'App',
  components:{
  	vHeader,
  	vNav
  }
}
</script>

<style></style>
