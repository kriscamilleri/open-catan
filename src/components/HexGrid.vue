<template>
  <g>
    <hex-tile
      v-for="hex in hexGrid"
      ref="hex"
      :key="hex.label"
      :x-offset="hex.xOffset"
      :y-offset="hex.yOffset"
      :label="hex.label"
      :grid-size="gridSize"
      @hex-clicked="hexClick"
      @path-clicked="pathClick"
      @node-clicked="nodeClick"
      @node-hovered="nodeHover"
      @node-mouseleft="nodeMouseleave"
></hex-tile>
  </g>
</template>

<script>
import HexTile from './HexTile.vue';

export default {
  name: 'HexGrid',
  components: {
    HexTile,
  },
  props: {
    gridSize: Number,
    width: Number,
    height: Number,
    renderTiles: Array,
    xHexCount: {
      default: 5,
      type: Number,
    },
    yHexCount: {
      default: 5,
      type: Number,
    },
  },
  data() {
    return {
      hexGrid: [],
      neighboursMap: [],
      hexPairList: [],
    };
  },
  methods: {
    drawHexGrid(xHexCount, yHexCount) {
      const hexagonGrid = [];
      let xOffset = 2;
      let counter = 1;
      for (let i = 0; i < xHexCount; i += 1) {
        for (let j = 0; j < yHexCount; j += 1) {
          if (this.renderTiles.includes(counter)) {
            const alternatingoffset = i % 2 === 0 ? 2 : 4;
            const yOffset = j * 4 + alternatingoffset;
            const label = counter.toString();
            const hexagonInitiator = { xOffset, yOffset, label };
            hexagonGrid.push(hexagonInitiator);
          }
          counter += 1;
        }
        xOffset += 3;
      }
      return hexagonGrid;
    },
    hexClick(hexTile) {
      this.$emit('hex-clicked', hexTile);
    },
    nodeClick(hexTile) {
      this.$emit('node-clicked', hexTile);
    },
    nodeHover(hexTile) {
      this.$emit('node-hovered', hexTile);
    },
    nodeMouseleave(hexTile) {
      this.$emit('node-mouseleft', hexTile);
    },
    pathClick(hexTile) {
      const modifiedHexTile = hexTile;
      // this.findNeighbouringTiles(hexTile);
      this.$emit('path-clicked', modifiedHexTile);
    },
    findNeighbouringTiles(hexTile) {
      const neighbours = [];
      neighbours.push(Number(hexTile.hex.label));
      for (let i = 0; i < this.hexPairList.length; i += 1) {
        if (
          this.hexPairList[i].path.x1 === Number(hexTile.path.x2.value)
          && this.hexPairList[i].path.x2 === Number(hexTile.path.x1.value)
          && this.hexPairList[i].path.y1 === Number(hexTile.path.y2.value)
          && this.hexPairList[i].path.y2 === Number(hexTile.path.y1.value)
        ) {
          neighbours.push(this.hexPairList[i].id);
        }
      }
      console.log(neighbours);
      return neighbours;
    },
  },
  mounted() {
    this.hexGrid = this.drawHexGrid(this.xHexCount, this.yHexCount);
    this.$nextTick(function () {
      const hexByPath = this.$refs.hex.map((hex) => {
        const id = Number(hex.hex.id);
        const paths = hex.getPaths();
        return { id, paths };
      });
      // populate store of all paths
      for (let i = 0; i < hexByPath.length; i += 1) {
        for (let j = 0; j < 6; j += 1) {
          const localPaths = hexByPath[i].paths[j];
          const mapped = {
            x1: localPaths.x1,
            x2: localPaths.x2,
            y1: localPaths.y1,
            y2: localPaths.y2,
          };
          const item = {
            id: hexByPath[i].id,
            path: mapped,
          };

          this.hexPairList.push(item);
        }
      }
      console.log(this.$refs.hex);
      this.$emit('hex-created', this.$refs.hex);
    });
  },
};
</script>
