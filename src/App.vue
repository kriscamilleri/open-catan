<template>
  <div id="app">
    <div class="svg-container"></div>
  </div>
</template>

<script>
import { SVG } from '@svgdotjs/svg.js';

export default {
  name: 'App',
  components: {},

  data() {
    return {
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
    createSVGSquareGrid(svgCanvas, xCount, yCount, gridSize) {
      for (let x = 0; x <= xCount; x += 1) {
        const originX = [x * gridSize, 0];
        const endX = [x * gridSize, yCount * gridSize];
        const firstLine = svgCanvas.line(
          originX[0],
          originX[1],
          endX[0],
          endX[1],
        );
        firstLine.stroke({ color: 'red', width: 2, linecap: 'round' });
      }
      for (let y = 0; y <= yCount; y += 1) {
        const origin = [0, y * gridSize];
        const end = [xCount * gridSize, y * gridSize];
        const firstLine = svgCanvas.line(origin[0], origin[1], end[0], end[1]);
        firstLine.stroke({ color: 'red', width: 2, linecap: 'round' });
      }
    },
    createHex(svgCanvas, gridSize, xOrigin, yOrigin, label) {
      const positions = [];
      for (let i = 0; i < 7; i += 1) {
        const xPosition = (this.offsets[i].x + xOrigin) * gridSize;
        const yPosition = (this.offsets[i].y + yOrigin) * gridSize;
        positions.push([xPosition, yPosition]);
      }

      const lines = [];
      const innerPosition = [];
      for (let i = 0; i < 6; i += 1) {
        const xPosition = (this.offsets[i].x * 0.8 + xOrigin) * gridSize;
        const yPosition = (this.offsets[i].y * 0.8 + yOrigin) * gridSize;
        innerPosition.push([xPosition, yPosition]);
      }
      for (let i = 0; i < 6; i += 1) {
        const xLastPosition = (this.offsets[i].x + xOrigin) * gridSize;
        const yLastPosition = (this.offsets[i].y + yOrigin) * gridSize;
        const xPosition = (this.offsets[i + 1].x + xOrigin) * gridSize;
        const yPosition = (this.offsets[i + 1].y + yOrigin) * gridSize;
        const line = svgCanvas
          .line(xLastPosition, yLastPosition, xPosition, yPosition)
          .stroke({ width: 9, color: 'green' })
          .on('click', () => {
            this.stroke({ color: 'red' });
          });
        lines.push(line);
      }

      // const perimiter = svgCanvas.polyline(positions)
      //     .fill("none")
      //     .stroke({
      //         color: 'blue', width: 2, linecap: 'round'
      //     })

      const innerPolygon = svgCanvas
        .polygon(innerPosition)
        .fill('green')
        .stroke({
          color: 'blue',
          width: 2,
          linecap: 'round',
        })
        .on('click', function () {
          this.fill({ color: 'violet' });
          console.log(label);
        });

      const centerDot = svgCanvas
        .circle((1 / 2) * gridSize)
        .move(
          xOrigin * gridSize - (1 / 4) * gridSize,
          yOrigin * gridSize - (1 / 4) * gridSize,
        )
        .fill('blue');

      const hexLabel = label || '';
      const centerText = svgCanvas
        .text(hexLabel)
        .move(xOrigin * gridSize, yOrigin * gridSize);

      const hexagon = {
        lines,
        center: centerDot,
        origin: [xOrigin, yOrigin],
        label,
        innerPolygon,
        centerText,
      };
      return hexagon;
    },
    createHexGrid(svgCanvas, xHexCount, yHexCount, gridSize) {
      const hexagonGrid = [];
      let xOffset = 2;
      let counter = 1;
      for (let i = 0; i < xHexCount; i += 1) {
        for (let j = 0; j < yHexCount; j += 1) {
          const alternatingoffset = i % 2 === 0 ? 2 : 4;
          const yOffset = j * 4 + alternatingoffset;
          const label = counter.toString();
          const hexagon = this.createHex(
            svgCanvas,
            gridSize,
            xOffset,
            yOffset,
            label,
          );
          hexagonGrid.push(hexagon);
          counter += 1;
        }
        xOffset += 3;
      }
      return hexagonGrid;
    },
  },
  mounted() {
    const gridSize = 25;
    const width = 500;
    const height = 500;
    const svgCanvas = SVG()
      .addTo('.svg-container')
      .size(width, height);
    this.createSVGSquareGrid(svgCanvas, 40, 40, gridSize);
    const hexGrid = this.createHexGrid(svgCanvas, 5, 5, gridSize);
    console.log(hexGrid);
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
