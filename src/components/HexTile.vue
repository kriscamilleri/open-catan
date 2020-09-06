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
      stroke-width="8"
      class="hex-outline"
      v-on:click="clickPath"
    />
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
    >{{hexText}}</text>
    <!-- <circle
      v-for="(node, key, index) in hex.nodes"
      :key="index"
      :cx="node.x"
      :cy="node.y"
      r="15"
      fill="transparent"
      stroke="transparent"
      stroke-width="0.5"
      @mouseover="hoverNode"
      @mouseleave="mouseleaveNode"
      :class="node.class"
    ></circle>-->
    <!-- <circle
      v-for="(node, key, index) in hex.nodes"
      :key="index"
      :cx="node.x"
      :cy="node.y"
      r="1"
      :fill="node.fill"
      stroke="grey"
      stroke-width="0.5"
      v-on:click="clickNode"
    ></circle>-->
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
polygon,
circle,
line {
  filter: drop-shadow(0.15rem 0.05rem 0.15rem rgba(0, 0, 0, 0.3));
}

line {
  cursor: pointer;
}
</style>
<script>
import HexNode from './HexNode.vue';

export default {
  name: 'HexTile',
  components: {
    HexNode,
  },
  props: {
    nodeStrokeWidth: Number,
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
  },
  created() {
    this.hex = this.generateHex(this.xOffset, this.yOffset, this.label);
    this.currentFill = this.fill;
    this.currentStroke = this.stroke;
  },
};
</script>
