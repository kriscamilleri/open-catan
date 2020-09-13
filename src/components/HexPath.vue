<template>
  <g>
    <line
      :key="id"
      :x1="x1"
      :y1="y1"
      :x2="x2"
      :y2="y2"
      :stroke="currentStroke"
      :stroke-width="currentStrokeWidth"
      class="hex-outline"
      @click="clickPath"
      @mouseover="hoverPath"
      @mouseleave="mouseleavePath"
    />
  </g>
</template>
<style>
line {
  filter: drop-shadow(0.15rem 0.05rem 0.15rem rgba(0, 0, 0, 0.3));
}

line {
  cursor: pointer;
}
</style>
<script>
export default {
  Name: 'HexPath',
  props: {
    id: Number,
    x1: Number,
    x2: Number,
    y1: Number,
    y2: Number,
    stroke: String,
    strokeWidth: Number,
  },
  data() {
    return {
      currentStroke: 'red',
      currentStrokeWidth: 1,
    };
  },
  methods: {
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
    hoverPath(event) {
      const path = {
        x1: event.srcElement.attributes.x1,
        y1: event.srcElement.attributes.y1,
        x2: event.srcElement.attributes.x2,
        y2: event.srcElement.attributes.y2,
      };
      this.$emit('path-hovered', {
        hex: this,
        path,
      });
    },
    mouseleavePath(event) {
      const path = {
        x1: event.srcElement.attributes.x1,
        y1: event.srcElement.attributes.y1,
        x2: event.srcElement.attributes.x2,
        y2: event.srcElement.attributes.y2,
      };
      this.$emit('path-mouseleft', {
        hex: this,
        path,
      });
    },
    setPathColor(color) {
      this.currentStroke = color;
    },
    setPathWidth(width) {
      this.currentStrokeWidth = width;
    },
  },
  created() {
    this.currentStroke = this.stroke;
    this.currentStrokeWidth = this.strokeWidth;
  },
};
</script>
