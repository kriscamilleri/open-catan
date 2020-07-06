<template>
  <g>
    <line
      v-for="line in lines"
      :key="line.id"
      :x1="line.x1"
      :y1="line.y1"
      :x2="line.x2"
      :y2="line.y2"
      :stroke="color"
      stroke-width="2"
      class="square-grid"
    />
  </g>
</template>

<script>
export default {
  name: 'SquareGrid',
  props: {
    gridWidth: Number,
    gridHeight: Number,
    gridSize: Number,
    color: {
      default: 'black',
      type: String,
    },
  },
  data() {
    return {
      lines: [],
    };
  },
  methods: {
    createSVGSquareGrid(xCount, yCount) {
      for (let x = 0; x <= xCount; x += 1) {
        const originX = [x * this.gridSize, 0];
        const endX = [x * this.gridSize, yCount * this.gridSize];
        const line = {
          x1: originX[0],
          y1: originX[1],
          x2: endX[0],
          y2: endX[1],
        };
        this.lines.push(line);
      }
      for (let y = 0; y <= yCount; y += 1) {
        const origin = [0, y * this.gridSize];
        const end = [xCount * this.gridSize, y * this.gridSize];
        const line = {
          x1: origin[0],
          y1: origin[1],
          x2: end[0],
          y2: end[1],
        };
        this.lines.push(line);
      }
    },
  },
  created() {
    this.createSVGSquareGrid(this.gridWidth, this.gridHeight);
  },
};
</script>
