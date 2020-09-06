<template>
  <g>
    <circle
      :cx="x"
      :cy="y"
      :r="radius"
      :fill="currentFill"
      :stroke="stroke"
      :stroke-width="strokeWidth"
      uselessattribute="2"
      :class="nodeClass + ' node-circle'"
    ></circle>
    <a href="#">
      <circle
        @mouseover="hoverNode"
        @mouseleave="mouseleaveNode"
        v-on:click="clickNode"
        :cx="x"
        :cy="y"
        fill="transparent"
        r="8"
      ></circle>
    </a>
    <!-- fill="rgba(193, 66, 66, 0.15)" -->
  </g>
</template>
<script>
export default {
  name: 'HexNode',
  props: {
    label: String,
    x: Number,
    y: Number,
    radius: {
      default: 4,
      type: Number,
    },
    fill: {
      default: 'blue',
      type: String,
    },
    stroke: {
      default: 'transparent',
      type: String,
    },
    strokeWidth: {
      default: 0.2,
      type: Number,
    },
  },
  data() {
    return {
      currentFill: String,
      nodeClass: String,
    };
  },
  methods: {
    hoverNode(event) {
      const xVal = Number(event.srcElement.getAttribute('cx'));
      const yVal = Number(event.srcElement.getAttribute('cy'));
      const node = {
        x: xVal, // keep numbers only
        y: yVal,
      };
      this.$emit('node-hovered', {
        hex: this,
        node,
      });
    },
    mouseleaveNode(event) {
      const xVal = Number(event.srcElement.getAttribute('cx'));
      const yVal = Number(event.srcElement.getAttribute('cy'));
      const node = {
        x: xVal, // keep numbers only
        y: yVal,
      };
      this.$emit('node-mouseleft', {
        hex: this,
        node,
      });
    },
    clickNode(event) {
      const xVal = Number(event.srcElement.getAttribute('cx'));
      const yVal = Number(event.srcElement.getAttribute('cy'));
      const node = {
        x: xVal, // keep numbers only
        y: yVal,
      };
      this.$emit('node-clicked', {
        hex: this,
        node,
      });
    },
    setNodeFill(color) {
      console.log(color);
      this.currentFill = color;
    },
    setNodeClass(text) {
      // , node
      this.nodeClass = text;
    },
  },
  created() {
    this.currentFill = this.fill;
  },
};
</script>
