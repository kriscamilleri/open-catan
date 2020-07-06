<template>
  <g :data-hex-index="label">
    <line
      v-for="path in hex.paths"
      :key="path.id"
      :x1="path.x1"
      :y1="path.y1"
      :x2="path.x2"
      :y2="path.y2"
      :stroke="path.strokeColor"
      stroke-width="10"
      class="hex-outline"
      v-on:click="clickPath"
    />
    <polygon
      :points="hex.polygonPath"
      :fill="currentFill"
      :stroke="currentStroke"
      v-on:click="clickHex"
    />
    <text :x="hex.origin.x" :y="hex.origin.y" fill="white">{{hex.id}}</text>
  </g>
</template>
<script>
export default {
  name: 'HexTile',
  props: {
    label: String,
    xOffset: Number,
    yOffset: Number,
    gridSize: Number,
    stroke: {
      default: 'red',
      type: String,
    },
    fill: {
      default: 'blue',
      type: String,
    },
  },
  data() {
    return {
      hex: {},
      currentStroke: 'red',
      currentFill: 'blue',
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
    generateHexPath(xOrigin, yOrigin) {
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
          strokeColor: this.currentStroke,
          fillColor: this.fillColor,
        };
        paths.push(path);
      }
      return paths;
    },
    generateHex(xOrigin, yOrigin, label) {
      const polygonPath = this.generateHexPolygon(
        xOrigin,
        yOrigin,
        this.hexStrokeColor,
        this.hexFillColor,
        'transparent',
      );

      const paths = this.generateHexPath(xOrigin, yOrigin, label);
      const hex = {
        id: label,
        paths,
        polygonPath,
        origin: { x: xOrigin * this.gridSize, y: yOrigin * this.gridSize },
      };
      return hex;
    },
    clickHex() {
      this.$emit('hex-clicked', {
        hex: this,
        polygon: this.hex.polygonPath,
      });
    },
    clickPath(event) {
      const path = {
        x1: event.srcElement.attributes.x1,
        y1: event.srcElement.attributes.y1,
        x2: event.srcElement.attributes.x2,
        y2: event.srcElement.attributes.y2,
      };
      this.$emit('path-clicked', {
        hex: this,
        path,
      });
    },
    setPolygonFill(color) {
      this.currentFill = color;
    },
    setPolygonStroke(color) {
      this.currentStroke = color;
    },
    setPathColor(color, line) {
      const chosenPath = this.hex.paths.find(
        (c) => c.x1 === Number(line.x1.nodeValue)
          && c.x2 === Number(line.x2.nodeValue)
          && c.y1 === Number(line.y1.nodeValue)
          && c.y2 === Number(line.y2.nodeValue),
      );
      chosenPath.strokeColor = color;
    },
    getPaths() {
      return this.hex.paths;
    },
  },
  created() {
    this.hex = this.generateHex(this.xOffset, this.yOffset, this.label);
    this.currentFill = this.fill;
    this.currentStroke = this.stroke;
  },
};
</script>
