<template>
  <g :data-hex-index="label">
    <hex-path
      v-for="path in hex.paths"
      :key="path.id"
      :x1="path.x1"
      :y1="path.y1"
      :x2="path.x2"
      :y2="path.y2"
      :stroke="path.strokeColor"
      :stroke-width="pathStrokeWidth"
      @path-clicked="pathClicked"
      @path-hovered="pathHovered"
      @path-mouseleft="pathMouseleft"
    ></hex-path>
    <polygon
      :points="hex.polygonPath"
      :fill="currentFill"
      :stroke="currentStroke"
      v-on:click="clickHex"
    />
    <text
      :x="hex.origin.x"
      :y="hex.origin.y"
      fill="white"
      dominant-baseline="middle"
      text-anchor="middle"
      v-on:click="clickHex"
    >{{hexText}}</text>
    <hex-node
      v-for="(node, key, index) in hex.nodes"
      :key="index"
      :x="node.x"
      :y="node.y"
      :stroke-width="nodeStrokeWidth"
      label="label"
      :radius="4"
      asdasd="asddsa"
      stroke="gray"
      fill="white"
      @node-hovered="nodeHovered"
      @node-mouseleft="nodeMouseleft"
      @node-clicked="nodeClicked"
    ></hex-node>
  </g>
</template>
<style scoped>
text {
  user-select: none;
  cursor: pointer;
}
polygon,
circle,
line {
  cursor: pointer;
  filter: drop-shadow(0.15rem 0.05rem 0.15rem rgba(0, 0, 0, 0.3));
}

line {
  cursor: pointer;
}
</style>
<script>
import HexNode from './HexNode.vue';
import HexPath from './HexPath.vue';

export default {
  name: 'HexTile',
  components: {
    HexNode,
    HexPath,
  },
  props: {
    nodeStrokeWidth: Number,
    pathStrokeWidth: Number,
    label: String,
    xOffset: Number,
    yOffset: Number,
    gridSize: Number,
    stroke: {
      default: 'transparent',
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
      currentStroke: 'transparent',
      currentFill: 'blue',
      hexText: '0',
      nodeFill: 'transparent',
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
    generateHexPolygon(xOrigin, yOrigin, modifier) {
      let polygonPath = '';
      for (let i = 0; i < 6; i += 1) {
        const xPosition = (this.offsets[i].x * modifier + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i].y * modifier + yOrigin) * this.gridSize;
        polygonPath += `${xPosition}, ${yPosition} `;
      }
      polygonPath.trimRight();

      return polygonPath;
    },
    generateHexNodes(xOrigin, yOrigin, modifier) {
      const nodes = [];
      for (let i = 0; i < 6; i += 1) {
        const xPosition = (this.offsets[i].x * modifier + xOrigin) * this.gridSize;
        const yPosition = (this.offsets[i].y * modifier + yOrigin) * this.gridSize;
        nodes.push({
          x: xPosition,
          y: yPosition,
          fill: this.nodeFill,
          class: '',
        });
      }

      return nodes;
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
      const polygonPath = this.generateHexPolygon(xOrigin, yOrigin, 0.8);
      const nodes = this.generateHexNodes(xOrigin, yOrigin, 1);
      const paths = this.generateHexPath(xOrigin, yOrigin, label);
      const hex = {
        id: label,
        paths,
        polygonPath,
        nodes,
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

    setPolygonFill(color) {
      this.currentFill = color;
    },
    setPolygonStroke(stroke) {
      this.currentStroke = stroke;
    },
    setPathStroke(stroke) {
      this.currentStroke = stroke;
    },
    setHexText(text) {
      this.hexText = text;
    },
    getPaths() {
      return this.hex.paths;
    },
    nodeHovered(node) {
      console.log('hovered');
      this.$emit('node-hovered', node);
    },
    nodeMouseleft(node) {
      this.$emit('node-mouseleft', node);
    },
    nodeClicked(node) {
      console.log(node);
      this.$emit('node-clicked', node);
    },
    pathHovered(path) {
      console.log('hovered');
      this.$emit('path-hovered', path);
    },
    pathMouseleft(path) {
      this.$emit('path-mouseleft', path);
    },
    pathClicked(path) {
      console.log(path);
      this.$emit('path-clicked', path);
    },
  },
  created() {
    this.hex = this.generateHex(this.xOffset, this.yOffset, this.label);
    this.currentFill = this.fill;
    this.currentStroke = this.stroke;
  },
};
</script>
