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
    ></hex-tile>
  </g>
</template>

<script>
import HexTile from "./HexTile.vue";

export default {
  name: "HexGrid",
  components: {
    HexTile
  },
  props: {
    gridSize: Number,
    width: Number,
    height: Number,
    renderTiles: Array,
    xHexCount: {
      default: 5,
      type: Number
    },
    yHexCount: {
      default: 5,
      type: Number
    },
    lineColor: {
      default: "green",
      type: String
    },
    hexStrokeColor: {
      default: "red",
      type: String
    },
    hexFillColor: {
      default: "blue",
      type: String
    }
  },
  data() {
    return {
      hexGrid: [],
      hexes: [],
      neighboursMap: [],
      hexPairList: []
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
      this.$emit("hex-clicked", hexTile);
    },
    pathClick(hexTile) {
      const modifiedHexTile = hexTile;
      const overlapList = [];
      for (let i = 0; i < this.hexPairList.length; i += 1) {
        //TODO: Continue here - want to be able to emit neighbouring tiles
        //Current issue is i need to understand relationship between diagonal values.
        //x1 will never equals x1 of some other value. it may be y2 or some other value.
        if (
          this.hexPairList[i].path.x1 === Number(hexTile.path.x1.value) &&
          this.hexPairList[i].path.x2 === Number(hexTile.path.x2) &&
          this.hexPairList[i].path.y1 === Number(hexTile.path.y1) &&
          this.hexPairList[i].path.y2 === Number(hexTile.path.y2)
        ) {
          overlapList.push(this.hexPairList[i].id);
          console.log(overlapList);
        }
      }
      this.$emit("path-clicked", modifiedHexTile);
    }
  },
  mounted() {
    this.hexGrid = this.drawHexGrid(this.xHexCount, this.yHexCount);
    this.$nextTick(function() {
      const hexByPath = this.$refs.hex.map(hex => {
        const id = Number(hex.hex.id);
        const paths = hex.getPaths();
        return { id, paths };
      });
      //populate store of all paths
      for (let i = 0; i < hexByPath.length; i += 1) {
        for (let j = 0; j < 6; j += 1) {
          const localPaths = hexByPath[i].paths[j];
          const mapped = {
            x1: localPaths.x1,
            x2: localPaths.x2,
            y1: localPaths.y1,
            y2: localPaths.y2
          };
          const item = {
            id: hexByPath[i].id,
            path: mapped
          };

          this.hexPairList.push(item);
        }
      }
    });
  }
};
</script>
