<template>
  <div id="app">
    <baiDumap></baiDumap>
  </div>
</template>

<script>
import baiDumap from "./components/baiDumap.vue";
export default {
  name: "App",
  components: {
    baiDumap,
  },
};
</script>

<style>
html,
body,
#app {
  margin: 0;
  height: 100%;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
