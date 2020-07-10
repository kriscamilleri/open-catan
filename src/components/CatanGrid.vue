<template>
  <div>
    <svg
      xmlns="http://www.w3.org/2000/svg"
      version="1.1"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      :width="width"
      :height="height"
    >
      <square-grid :gridSize="25" :grid-width="17" :grid-height="21" />
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
    <div>
      <div class="hud">
        <div class="hud-header"></div>
        <div class="hud-stat" id="ownedTiles"></div>
        <div class="hud-cards">
          <div class="hud-card" id="wheat">
            <h2>Wheat</h2>
          </div>
          <div class="hud-card" id="brick">
            <h2>Brick</h2>
          </div>
          <div class="hud-card" id="wood">
            <h2>Wood</h2>
          </div>
          <div class="hud-card" id="stone">
            <h2>Stone</h2>
          </div>
          <div class="hud-card" id="sheep">
            <h2>Sheep</h2>
          </div>
        </div>
      </div>
      <h2></h2>
    </div>
  </div>
</template>
<style scoped>
.hud-card {
  max-width: 200px;
  min-width: 100px;
  width: 20vw;
  margin: 0.25rem;
  border-radius: 1rem;
  height: 200px;
  display: flex;
  text-align: center;
}

#wheat {
  border: 0.5rem var(--yellow) solid;
}
#brick {
  border: 0.5rem var(--orange) solid;
}
#wood {
  border: 0.5rem var(--brown) solid;
}
#stone {
  border: 0.5rem var(--grey) solid;
}
#sheep {
  border: 0.5rem var(--green) solid;
}
.hud-card h2 {
  text-align: center;
  width: 100%;
  height: min-content;
}
.hud-cards {
  flex-direction: row;
  justify-content: center;
  display: flex;
}
</style>
<script>
import HexGrid from './HexGrid.vue';
import SquareGrid from './SquareGrid.vue';

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
    SquareGrid,
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
