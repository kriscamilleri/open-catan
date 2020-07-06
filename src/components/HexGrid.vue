<template>
  <g>
    <g v-for="hex in hexGrid" :key="hex.id" :data-hex-index="hex.id">
      <line
        v-for="line in hex.lines"
        :key="line.id"
        :x1="line.x1"
        :y1="line.y1"
        :x2="line.x2"
        :y2="line.y2"
        :stroke="hexStrokeColor"
        stroke-width="2"
        class="hex-outline"
      />
      <polygon :points="hex.polygonPath" :fill="hexFillColor" />
      <text :x="hex.origin.x" :y="hex.origin.y" fill="white">{{hex.id}}</text>
    </g>
  </g>
</template>

<script>
export default {
  name: 'HexGrid',
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
    lineColor: {
      default: 'green',
      type: String,
    },
    hexStrokeColor: {
      default: 'red',
      type: String,
    },
    hexFillColor: {
      default: 'blue',
      type: String,
    },
    centerStrokeColor: {
      default: 'orange',
      type: String,
    },
    centerFillColor: {
      default: 'purple',
      type: String,
    },
  },
  data() {
    return {
      hexGrid: [],
      offsets: [
        {
          label: 'top-start',
          x: -1,
          y: -2,
        },
        {
          label: 'top-right',
          x: 1,
          y: -2,
        },
        {
          label: 'bottom-right',
          x: 2,
          y: 0,
        },
        {
          label: 'bottom',
          x: 1,
          y: 2,
        },
        {
          label: 'bottom-left',
          x: -1,
          y: 2,
        },
        {
          label: 'top-left',
          x: -2,
          y: 0,
        },
        {
          label: 'top-end',
          x: -1,
          y: -2,
        },
      ],
    };
  },
  methods: {
    generateHexPolygon(xOrigin, yOrigin) {
      let polygonPath = '';
      for (let i = 0; i < 6; i += 1) {
        const xPosition = (this.offsets[i].x * 0.8 + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i].y * 0.8 + yOrigin) * this.gridSize;
        polygonPath += `${xPosition}, ${yPosition} `;
      }
      polygonPath.trimRight();

      return polygonPath;
    },
    generateHexPath(xOrigin, yOrigin, strokeColor, fillColor) {
      const paths = [];
      for (let i = 0; i < 6; i += 1) {
        const xLastPosition = (this.offsets[i].x + xOrigin) * this.gridSize;
        const yLastPosition = (this.offsets[i].y + yOrigin) * this.gridSize;
        const xPosition = (this.offsets[i + 1].x + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i + 1].y + yOrigin) * this.gridSize;

        const path = {
          x1: xPosition,
          y1: yPosition,
          x2: xLastPosition,
          y2: yLastPosition,
          id: i,
          strokeColor,
          fillColor,
        };
        paths.push(path);
      }
      return paths;
    },
    drawHex(xOrigin, yOrigin, label) {
      const polygonPath = this.generateHexPolygon(
        xOrigin,
        yOrigin,
        this.hexStrokeColor,
        this.hexFillColor,
        'transparent',
      );

      const lines = this.generateHexPath(
        xOrigin,
        yOrigin,
        this.lineColor,
        this.hexFillColor,
        label,
      );
      const hexagon = {
        id: label,
        lines,
        polygonPath,
        origin: { x: xOrigin * this.gridSize, y: yOrigin * this.gridSize },
      };
      return hexagon;
    },
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
            const hexagon = this.drawHex(xOffset, yOffset, label);
            hexagonGrid.push(hexagon);
          }
          counter += 1;
        }
        xOffset += 3;
      }
      return hexagonGrid;
    },
  },
  mounted() {
    this.hexGrid = this.drawHexGrid(this.xHexCount, this.yHexCount);
  },
};
</script>
