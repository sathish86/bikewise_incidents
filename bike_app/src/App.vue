<template>
  <div id="app">
    <BikeWise/>
  </div>
</template>

<script>
import BikeWise from './components/BikeWise.vue'

export default {
  name: 'app',
  components: {
    BikeWise
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
