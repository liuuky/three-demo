<template>
  <div id="app">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <!-- <Vehicle /> -->
    <!-- <Cube /> -->
    <Car />
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
// import Vehicle from './components/Vehicle.vue'
// import Cube from './components/Cube.vue'
import Car from './components/Car.vue'

export default {
  name: 'App',
  components: {
    // HelloWorld,
    // Vehicle,
    // Cube,
    Car,
  }
}
</script>

<style>
/* #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
} */
</style>
