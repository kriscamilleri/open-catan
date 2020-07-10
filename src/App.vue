<template>
  <div id="app">
    <catan-grid :width="400" :height="500"></catan-grid>
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

:root {
  --red: rgba(239, 83, 80, 1);
  --white: rgba(250, 250, 250, 1);
  --brown: rgba(141, 110, 99, 1);
  --grey: rgba(120, 144, 156, 1);
  --green: rgba(102, 187, 106, 1);
  --yellow: rgba(255, 202, 40, 1);
  --orange: rgba(255, 112, 67, 1);
}
</style>
