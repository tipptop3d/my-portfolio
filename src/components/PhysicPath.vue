<template>
  <div class="container">
    <svg ref="svgElement" preserveAspectRatio="xMinYMid meet"
      :viewBox="`0 ${-viewBoxWidth / 2} ${viewBoxWidth} ${viewBoxHeight}`" display="block">
      <path ref="pathElement" :d="path"></path>
    </svg>
    <div class="circle" :style="{ '--offset-path': `path('${scaledPath}')` }"></div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import * as d3 from 'd3'

const viewBoxWidth = 16
const viewBoxHeight = 16
const path = ref('M 0 0 Q 7 5 8 0 Q 9 -4 16 0')

const svgElement = ref<SVGElement | null>(null)
const pathElement = ref<SVGPathElement | null>(null)
const scaledPath = ref<string | null>()
// const motionPath = new ResponsiveMotionPath({
//   viewBoxHeight,
//   viewBoxWidth,
//   path
// })

type xy = [number, number]

function convertPathToData(path: SVGPathElement) {
  const data: xy[] = []

  for (let p = 0; p < path.getTotalLength(); p++) {
    const { x, y } = path.getPointAtLength(p);
    data.push([x, y])
  }
  return data
}

function getMaximums(data: xy[]): xy {
  const x_points = data.map(point => point[0])
  const y_points = data.map(point => point[1])
  return [Math.max(...x_points), Math.max(...y_points)]
}

function getRatios(maxs: xy, width: number, height: number): xy {
  return [maxs[0] / width, maxs[1] / height]
}


onMounted(() => {
  const { width, height } = svgElement.value!.getBoundingClientRect()
  const data = convertPathToData(pathElement.value!)
  const maximums = getMaximums(data)
  const ratios = getRatios(maximums, viewBoxWidth, viewBoxHeight)

  const widthRatio = (viewBoxHeight - viewBoxWidth) / viewBoxHeight
  const widthOffset = (ratios[0] * width) / 2

  const heightRatio = (viewBoxWidth - viewBoxHeight) / viewBoxWidth
  const heightOffset = (ratios[1] * height) / 2

  const xScale = d3
    .scaleLinear()
    .domain([0, maximums[0]])
    .range([widthOffset, width * widthRatio - widthOffset])
  const yScale = d3
    .scaleLinear()
    .domain([0, maximums[1]])
    .range([heightOffset, height * heightRatio - heightOffset])
  const scaled_points: xy[] = data.map(point => [
    xScale(point[0]), yScale(point[1])
  ])
  scaledPath.value = d3.line()(scaled_points)
})

</script>

<style scoped>
.container {
  height: 500px;
  width: 500px;
  outline: 2px black solid;
  position: relative;
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
  top: 100px;
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