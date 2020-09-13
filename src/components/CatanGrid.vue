<template>
  <div id="catan-parent">
    <svg
      xmlns="http://www.w3.org/2000/svg"
      version="1.1"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      :width="width"
      :height="height"
      stroke-linecap="round"
      ref="svg"
    >
      <square-grid v-if="showSquareGrid" :gridSize="25" :grid-width="17" :grid-height="21" />

      <hex-grid
        :gridSize="25"
        :width="width"
        :height="height"
        :render-tiles="catanTiles"
        :xHexCount="5"
        :yHexCount="5"
        :node-stroke-width="0.5"
        @hex-clicked="hexClick"
        @node-clicked="nodeClick"
        @node-hovered="nodeHover"
        @node-mouseleft="nodeMouseleft"
        @path-clicked="pathClick"
        @path-hovered="pathHover"
        @path-mouseleft="pathMouseleft"
        @hex-created="processHexGrid"
      />
    </svg>

    <div id="overlay">
      <div class="hud">
        <div class="hud-header"></div>
        <div class="hud-stat" id="ownedTiles"></div>
        <div class="hud-cards">
          <div class="hud-card" id="wheat">
            <h2>Wheat</h2>
          </div>
          <div class="hud-card" id="brick">
            <h2>Brick</h2>
          </div>
          <div class="hud-card" id="wood">
            <h2>Wood</h2>
          </div>
          <div class="hud-card" id="stone">
            <h2>Stone</h2>
          </div>
          <div class="hud-card" id="sheep">
            <h2>Sheep</h2>
          </div>
        </div>
      </div>
      <h2></h2>
    </div>
  </div>
</template>
<style>
.hover-enter-node {
  animation: floatover 0.6s cubic-bezier(0.24, 0.24, 0.21, 1.06) alternate
    infinite;
}
.hover-exit-node {
  animation: floatexit 0.6s cubic-bezier(0.24, 0.24, 0.21, 1.06) alternate 1;
}
@keyframes floatover {
  0% {
    transform: scale(1);
    transform-origin: 50% 50%;
    transform-box: fill-box;
  }

  100% {
    transform: scale(2);
    transform-origin: 50% 50%;
    transform-box: fill-box;
  }
}

@keyframes floatexit {
  0% {
    transform: scale(2);
    transform-origin: 50% 50%;
    transform-box: fill-box;
  }

  100% {
    transform: scale(1);
    transform-origin: 50% 50%;
    transform-box: fill-box;
  }
}
#overlay {
  position: absolute;
  top: 0;
}
svg {
  margin: 0 auto;
}
.hud {
  display: flex;
  flex-direction: column;
}
#catan-parent {
  --min-hud-height: 150px;
  --hud-height: 100vh;
  --min-hud-width: 100px;
  --hud-width: 15vw;
  display: flex;
  justify-content: flex-start;
  flex-direction: row;
}
#catan-parent svg {
  transform-origin: top center; /* add this in */
  /* margin-left: max(2.5 * var(--min-hud-width), 2.5 * var(--hud-width)); */
  /* height: 80vh; */
  /* width: 100%;
  height: 100%;
  min-height: 60vh;
  min-width: 100%; */
}
.hud-cards {
  /* top: calc(100vh - max(var(--min-hud-height), var(--hud-height))); */
  margin: 0 auto;
  flex-wrap: wrap;
  max-width: 100vw;
  overflow-wrap: anywhere;
  justify-content: flex-start;
}
.hud-card {
  min-width: 100px;
  min-height: 100px;
  max-width: 15rem;
  width: 15vw;
  margin: 0.25rem;
  border-radius: 1rem;
  height: 100%;
  text-align: center;
  align-self: baseline;
}
#wheat {
  border: 0.5rem var(--yellow) solid;
}
#brick {
  border: 0.5rem var(--orange) solid;
}
#wood {
  border: 0.5rem var(--brown) solid;
}
#stone {
  border: 0.5rem var(--grey) solid;
}
#sheep {
  border: 0.5rem var(--green) solid;
}
.hud-card h2 {
  text-align: center;
  width: 100%;
  height: min-content;
}
.hud-cards {
  flex-direction: column;
  justify-content: center;
  display: flex;
}
</style>
<script>
import HexGrid from './HexGrid.vue';
import SquareGrid from './SquareGrid.vue';

export default {
  name: 'App',
  props: {
    width: {
      default: 400,
      type: Number,
    },
    height: {
      default: 500,
      type: Number,
    },
  },
  components: {
    HexGrid,
    SquareGrid,
  },
  data() {
    return {
      showSquareGrid: false,
      catanTiles: [
        2,
        3,
        4,
        6,
        7,
        8,
        9,
        11,
        12,
        13,
        14,
        15,
        16,
        17,
        18,
        19,
        22,
        23,
        24,
      ],
      hexTiles: [],
      myColor: {
        name: 'blue',
        rgba: 'rgba(82, 80, 239, 1)',
      },
      colors: [
        {
          name: 'lightRed',
          rgba: 'rgba(239, 83, 80, 0.7)',
        },
        {
          name: 'red',
          rgba: 'rgba(239, 83, 80, 1)',
        },
        {
          name: 'lightWhite',
          rgba: 'rgba(250, 250, 250, 0.7)',
        },
        {
          name: 'white',
          rgba: 'rgba(250, 250, 250, 1)',
        },
        {
          name: 'lightBrown',
          rgba: 'rgba(141, 110, 99, 0.7)',
        },
        {
          name: 'brown',
          rgba: 'rgba(141, 110, 99, 1)',
        },
        {
          name: 'lightGrey',
          rgba: 'rgba(120, 144, 156, 0.7)',
        },
        {
          name: 'grey',
          rgba: 'rgba(120, 144, 156, 1)',
        },
        {
          name: 'lightGreen',
          rgba: 'rgba(102, 187, 106, 0.7)',
        },
        {
          name: 'green',
          rgba: 'rgba(102, 187, 106, 1)',
        },
        {
          name: 'lightYellow',
          rgba: 'rgba(255, 202, 40, 0.7)',
        },
        {
          name: 'yellow',
          rgba: 'rgba(255, 202, 40, 1)',
        },
        {
          name: 'lightOrange',
          rgba: 'rgba(255, 112, 67, 0.7)',
        },
        {
          name: 'orange',
          rgba: 'rgba(255, 112, 67, 1)',
        },
      ],
      windowHeight: 1024,
      windowWidth: 1024,
    };
  },
  methods: {
    hexClick(event) {
      const currentColor = event.hex.currentFill;
      const result = this.colors.find((c) => c.rgba === currentColor);
      const newColor = result.name.includes('light')
        ? this.colors.find(
          (c) => c.name === result.name.replace('light', '').toLowerCase(),
        )
        : this.colors.find((c) => c.name.toLowerCase().includes(`light${result.name}`));

      event.hex.setPolygonFill(newColor.rgba);
    },
    pathClick(event) {
      const thisEvent = event;
      console.log(event);
      console.log('path-click', event);
      thisEvent.hex.isClicked = true;
      event.hex.setPathWidth(8);
      event.hex.setPathColor(this.myColor.rgba, event.path);
    },
    pathHover(event) {
      const thisEvent = event;
      console.log('path-hover', event);
      thisEvent.hex.isHovered = true;
      if (!event.hex.isClicked) {
        event.hex.setPathWidth(8);
        event.hex.setPathColor('rgba(45, 55, 72, 0.2)', event.path);
      }
    },
    pathMouseleft(event) {
      console.log('path-mouseleft', event);
      const thisEvent = event;
      if (event.hex.isHovered && !event.hex.isClicked) {
        thisEvent.hex.isHovered = false;
        event.hex.setPathWidth(8);
        event.hex.setPathColor('transparent', event.path);
      }
    },
    nodeClick(event) {
      console.log(event);
      event.hex.setNodeRadius(8);
      // event.hex.setNodeFill(this.myColor.rgba, event.node);
    },
    nodeHover(event) {
      console.log(event);
      // event.hex.setNodeFill(this.myColor.rgba, event.node);
      event.hex.setNodeClass('hover-enter-node', event.node);
    },
    nodeMouseleft(event) {
      console.log(event);
      // event.hex.setNodeFill('transparent', event.node);
      event.hex.setNodeClass('hover-exit-node', event.node);
    },
    processHexGrid(hexTiles) {
      const self = this;
      console.log(self);

      for (let i = 0; i < hexTiles.length; i += 1) {
        const randomColor = this.getRandomInt(5);
        let color = '';
        switch (randomColor) {
          case 0: {
            color = self.colors.find((c) => c.name === 'red');
            break;
          }
          case 1: {
            color = self.colors.find((c) => c.name === 'yellow');
            break;
          }
          case 2: {
            color = self.colors.find((c) => c.name === 'orange');
            break;
          }
          case 3: {
            color = self.colors.find((c) => c.name === 'green');
            console.log(color);
            break;
          }
          case 4: {
            color = self.colors.find((c) => c.name === 'grey');
            break;
          }

          default:
            break;
        }
        hexTiles[i].setPolygonFill(color.rgba);
        const randomNumber = this.getRandomInt(12) + 1;
        hexTiles[i].setHexText(randomNumber.toString());
      }
      this.hexTiles = hexTiles;
    },
    getRandomInt(max) {
      return Math.floor(Math.random() * Math.floor(max));
    },
    adjustSvgScale() {
      // console.log(this.windowWidth, this.windowHeight);
      const widthHeightRatio = this.getTileWidthHeightRatio();
      const minRatio = this.windowHeight > this.windowWidth
        ? this.windowWidth / (this.width / widthHeightRatio)
        : this.windowHeight / this.height;

      const transform = `scale(${minRatio}) `;
      this.setSvgScale(transform);
    },
    setSvgScale(transform) {
      if (this.$refs.svg) {
        this.$refs.svg.style.transform = transform;
      }
    },
    getTileWidthHeightRatio() {
      if (this.$refs.svg) {
        return this.$refs.svg.clientWidth / this.$refs.svg.clientHeight;
      }
      return 1;
    },
    toTitleCase(str) {
      return str.replace(
        /\w\S*/g,
        (txt) => txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase(),
      );
    },
  },
  created() {},
  mounted() {
    window.addEventListener('resize', () => {
      this.$nextTick(() => {
        this.windowHeight = window.innerHeight;
        this.windowWidth = window.innerWidth;
        this.adjustSvgScale();
      });
    });
    this.$nextTick(() => {
      this.windowHeight = window.innerHeight;
      this.windowWidth = window.innerWidth;
      this.adjustSvgScale();
    });
  },
};
</script>
