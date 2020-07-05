<template>
  <div id="app">
    <catan-grid></catan-grid>
  </div>
</template>

<script>
import CatanGrid from './components/CatanGrid.vue';

export default {
  name: 'App',
  components: {
    CatanGrid,
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
