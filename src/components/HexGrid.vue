<template>
  <div class="svg-container"></div>
</template>

<script>
import { SVG } from '@svgdotjs/svg.js';

export default {
  name: 'HexGrid',
  props: {
    gridSize: Number,
    width: Number,
    height: Number,
  },
  data() {
    return {
      svgCanvas: Object,
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
    createSVGSquareGrid(xCount, yCount) {
      for (let x = 0; x <= xCount; x += 1) {
        const originX = [x * this.gridSize, 0];
        const endX = [x * this.gridSize, yCount * this.gridSize];
        const firstLine = this.svgCanvas.line(
          originX[0],
          originX[1],
          endX[0],
          endX[1],
        );
        firstLine.stroke({ color: 'red', width: 2, linecap: 'round' });
      }
      for (let y = 0; y <= yCount; y += 1) {
        const origin = [0, y * this.gridSize];
        const end = [xCount * this.gridSize, y * this.gridSize];
        const firstLine = this.svgCanvas.line(
          origin[0],
          origin[1],
          end[0],
          end[1],
        );
        firstLine.stroke({ color: 'red', width: 2, linecap: 'round' });
      }
    },
    drawHexTile(xOrigin, yOrigin, strokeColor, fillColor, clickColor) {
      const innerPosition = [];

      for (let i = 0; i < 6; i += 1) {
        const xPosition = (this.offsets[i].x * 0.8 + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i].y * 0.8 + yOrigin) * this.gridSize;
        innerPosition.push([xPosition, yPosition]);
      }
      const innerPolygon = this.svgCanvas
        .polygon(innerPosition)
        .fill({ color: fillColor })
        .stroke({
          color: strokeColor,
          width: 2,
          linecap: 'round',
        })
        .on('click', function () {
          this.fill({ color: clickColor });
        });
      return innerPolygon;
    },
    generateLines(xOrigin, yOrigin, strokeColor, fillColor) {
      const innerPosition = [];
      const lines = [];

      for (let i = 0; i < 6; i += 1) {
        const xPosition = (this.offsets[i].x * 0.8 + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i].y * 0.8 + yOrigin) * this.gridSize;
        innerPosition.push([xPosition, yPosition]);
      }
      for (let i = 0; i < 6; i += 1) {
        const xLastPosition = (this.offsets[i].x + xOrigin) * this.gridSize;
        const yLastPosition = (this.offsets[i].y + yOrigin) * this.gridSize;
        const xPosition = (this.offsets[i + 1].x + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i + 1].y + yOrigin) * this.gridSize;
        const line = this.svgCanvas
          .line(xLastPosition, yLastPosition, xPosition, yPosition)
          .stroke({ width: 9, color: strokeColor })
          .on('click', function () {
            this.stroke({ color: fillColor });
          });
        lines.push(line);
      }
      return lines;
    },
    drawCenterDot(xOrigin, yOrigin, strokeColor, fillColor) {
      const centerDot = this.svgCanvas
        .circle((1 / 2) * this.gridSize)
        .move(
          xOrigin * this.gridSize - (1 / 4) * this.gridSize,
          yOrigin * this.gridSize - (1 / 4) * this.gridSize,
        )
        .fill(fillColor)
        .stroke(strokeColor);
      return centerDot;
    },
    drawHex(xOrigin, yOrigin, label) {
      const innerPolygon = this.drawHexTile(
        xOrigin,
        yOrigin,
        'red',
        'green',
        'blue',
      );
      const lines = this.generateLines(xOrigin, yOrigin, 'orange', 'violet');

      const centerDot = this.drawCenterDot(xOrigin, yOrigin, 'red', 'green');
      const hexLabel = label.toString() || '';
      const centerText = this.svgCanvas
        .text(hexLabel)
        .move(xOrigin * this.gridSize, yOrigin * this.gridSize);

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
    drawHexGrid(xHexCount, yHexCount) {
      const hexagonGrid = [];
      let xOffset = 2;
      let counter = 1;
      for (let i = 0; i < xHexCount; i += 1) {
        for (let j = 0; j < yHexCount; j += 1) {
          const alternatingoffset = i % 2 === 0 ? 2 : 4;
          const yOffset = j * 4 + alternatingoffset;
          const label = counter.toString();
          const hexagon = this.drawHex(xOffset, yOffset, label);
          hexagonGrid.push(hexagon);
          counter += 1;
        }
        xOffset += 3;
      }
      return hexagonGrid;
    },
    tileClicked() {},
  },
  mounted() {
    this.svgCanvas = SVG()
      .addTo('.svg-container')
      .size(this.width, this.height);
    this.createSVGSquareGrid(40, 40);
    this.hexGrid = this.drawHexGrid(5, 5);
  },
};
</script>
