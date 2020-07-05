<template>
  <div id="app">
    <hex-grid :gridSize="25" :width="500" :height="500"></hex-grid>
  </div>
</template>

<script>
import HexGrid from './components/HexGrid.vue';

export default {
  name: 'App',
  components: {
    HexGrid,
  },
};
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
