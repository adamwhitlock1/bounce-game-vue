<template>
  <div id="app">
    <Box />
  </div>
</template>

<script>
import Box from "./components/Box.vue";

export default {
  name: "app",
  components: {
    Box
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#app {
  height: 100vh;
  width: 100vw;
}
</style>
