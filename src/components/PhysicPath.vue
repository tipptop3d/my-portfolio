<template>
  <div class="container" :style="{ width: `${width}px`, height: `${height}px` }">
    <svg ref="svgElement" preserveAspectRatio="xMinYMid meet"
      :viewBox="`0 ${-viewBoxHeight / 2} ${viewBoxWidth} ${viewBoxHeight}`" display="block">
      <path ref="pathElement" :d="path"></path>
      <circle r="0.5" :style="{ '--offset-path': `path('${path}')` }"></circle>
    </svg>
  </div>
  <div>
    <input id="width" type="text" v-model="width" />
    <label for="width">Width</label>
    <input id="height" type="text" v-model="height" />
    <label for="height">Height</label>
    <input id="viewBoxWidth" type="text" v-model="viewBoxWidth" />
    <label for="viewBoxWidth">ViewBoxWidth</label>
    <input id="viewBoxHeight" type="text" v-model="viewBoxHeight" />
    <label for="viewBoxHeight">ViewBoxHeight</label>
    <input id="path" type="text" v-model="path" />
    <label for="path">Path</label>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed } from 'vue'
import { SVGPathData } from 'svg-pathdata'

const width = ref(1920);
const height = ref(1080);

const viewBoxWidth = ref(16)
const viewBoxHeight = ref(9)

const pathElement = ref<SVGPathElement | null>(null)

const path = ref('M 0 0 Q 7 5 8 0 Q 9 -4 16 0')

path.value = "M 0 0 L 16 4.5"

const pathData = computed(() => new SVGPathData(path.value))
const offset = ref(0)

const gravity = 9.81 // m/s^2
const mass = 1000 // g
const slope = 29.5 // degree

function acceleration() {
  return mass * Math.tan(slope * Math.PI / 180)
}

onMounted(() => {
})

</script>

<style scoped>
.container {
  outline: 2px black solid;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}


svg {
  max-height: 100%;
  max-width: 100%;
  stroke: black;
  stroke-width: 0.1;
  fill: none;
  outline: 2px red solid;
}

circle {
  stroke: none;
  fill: cyan;
  offset-path: var(--offset-path);
  animation: move 3000ms infinite alternate ease-in-out;
}

input,
label {
  padding-right: 16px;
}

@keyframes move {
  0% {
    offset-distance: 0%;
  }

  100% {
    offset-distance: 100%;
  }
}
</style>