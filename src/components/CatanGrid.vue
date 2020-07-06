<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
    xmlns:xlink="http://www.w3.org/1999/xlink"
    :width="width"
    :height="height"
  >
    <!-- <square-grid :gridSize="25" :grid-width="17" :grid-height="21" /> -->
    <hex-grid
      :gridSize="25"
      :width="width"
      :height="height"
      :render-tiles="catanTiles"
      :xHexCount="5"
      :yHexCount="5"
      @hex-clicked="hexClick"
      @path-clicked="pathClick"
    />
  </svg>
</template>

<script>
import HexGrid from './HexGrid.vue';
// import SquareGrid from './SquareGrid.vue';

export default {
  name: 'App',
  props: {
    width: {
      default: 400,
      type: Number,
    },
    height: {
      default: 500,
      type: Number,
    },
  },
  components: {
    HexGrid,
    // SquareGrid,
  },
  data() {
    return {
      catanTiles: [
        2,
        3,
        4,
        6,
        7,
        8,
        9,
        11,
        12,
        13,
        14,
        15,
        16,
        17,
        18,
        19,
        22,
        23,
        24,
      ],
    };
  },
  methods: {
    hexClick(event) {
      event.hex.setPolygonFill('red');
    },
    pathClick(event) {
      event.hex.setPathColor('green', event.path);
    },
  },
};
</script>
