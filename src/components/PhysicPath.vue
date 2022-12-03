<template>
  <div class="container" :style="{ width: `${width}px`, height: `${height}px` }">
    <svg ref="svgElement" preserveAspectRatio="xMinYMid meet"
      :viewBox="`0 ${-viewBoxHeight / 2} ${viewBoxWidth} ${viewBoxHeight}`" display="block">
      <path ref="pathElement" :d="path"></path>
    </svg>
    <div class="circle" :style="{ '--offset-path': `path('${scaledPath}')` }"></div>
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
import { ref, onMounted, watch, computed } from 'vue'

const width = ref(500);
const height = ref(500);


const viewBoxWidth = ref(16)
const viewBoxHeight = ref(16)
const path = ref('M 0 0 Q 7 5 8 0 Q 9 -4 16 0')

const svgElement = ref<SVGElement | null>(null)
const pathElement = ref<SVGPathElement | null>(null)

const scaledPath = computed(() => {
  if (svgElement.value == null) {
    return null
  }
  const factor = width.value / viewBoxWidth.value

  return path.value.split(/[, ]/).filter(sym => sym !== '').map(sym => {
    if (!Number.isNaN(+sym)) {
      return +sym * factor
    } else {
      return sym
    }
  }).join(' ')
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

.circle {
  border-radius: 50%;
  width: 30px;
  height: 30px;
  background-color: aqua;
  position: absolute;
  top: calc(v-bind(height) / 2 * 1px);
  left: 0;
  offset-path: var(--offset-path);
  animation: move 3000ms infinite alternate ease-in-out;
  ;
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