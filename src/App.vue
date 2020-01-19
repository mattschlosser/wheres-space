<template>
  <div id="app">
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
  min-height: 100vh;
}
</style>
