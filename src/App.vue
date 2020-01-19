<template>
  <div id="app">
    <div class='navbar navbar-dark bg-dark fixed-top'>
      <div class='navbar-brand text-white ml-3'>Wheres Space</div>
    </div>
    <div class='navbar'>
      &nbsp; <!-- fake nvabar -->
    </div>
    <div class='jumbotron'>
      <p>Where's Space helps you find a quiet place at your university</p>
    </div>
    <TheSchedule></TheSchedule>
  </div>
</template>

<script>
import TheSchedule from './components/TheSchedule.vue'

export default {
  name: 'app',
  components: {
    TheSchedule
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
}
</style>
