<template>
    <div>
        <hex-grid :gridSize="25" :width="500" :height="500" :render-tiles="catanTiles"></hex-grid>
    </div>
</template>

<script>
import HexGrid from './HexGrid.vue';

export default {
  name: 'App',
  components: {
    HexGrid,
  },
  data() {
    return {
      catanTiles: [2, 3, 4, 6, 7, 8, 9, 11, 12, 13, 14, 15, 16, 17, 18, 19, 22, 23, 24],
    };
  },
};
</script>
