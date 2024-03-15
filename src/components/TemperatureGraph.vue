<script setup lang="ts">
import { onMounted, computed } from 'vue'
import * as d3 from 'd3'

const props = defineProps({
  data: {
    type: Array,
    required: true,
    default: () => [
      {
        time: '2022-01-01T00:00:00Z',
        temperature: 0,
        apparent_temperature: 0
      }
    ]
  }
})

const width = 800
const height = 300
const marginTop = 20
const marginRight = 20
const marginBottom = 30
const marginLeft = 40


const tempData = computed(() => props.data.map(d => d.temperature))
const apparentTempData = computed(() => props.data.map(d => d.apparent_temperature))
const allTemps = computed(() => [...tempData.value, ...apparentTempData.value])

onMounted(() => {
  const tempContainer = d3.select("#temperature")
  // Declare the x (horizontal position) scale.
  const x = d3.scaleUtc()
    .domain([new Date(props.data[0].time), new Date(props.data[props.data.length - 1].time)])
    .range([marginLeft, width - marginRight])

  // Declare the y (vertical position) scale.
  const y = d3.scaleLinear()
    .domain([d3.min(allTemps.value) - 1, d3.max(allTemps.value)])
    .range([height - marginBottom, marginTop])

  // Create the SVG container.
  const svg = tempContainer.append("svg")
    .attr("width", width)
    .attr("height", height);

  // Add the x-axis.
  svg.append("g")
    .attr("transform", `translate(0,${height - marginBottom})`)
    .call(d3.axisBottom(x));

  // Add the y-axis.
  svg.append("g")
    .attr("transform", `translate(${marginLeft},0)`)
    .call(d3.axisLeft(y));

  // Add the temperature line
  svg.append("path")
    .datum(props.data)
    .attr("fill", "none")
    .attr("stroke", "steelblue")
    .attr("stroke-width", 1.5)
    .attr("d", d3.line()
      .x((d) => x(d.time))
      .y((d) => y(d.temperature))
    )

  // Add temperature the points
  svg.append("g")
    .selectAll("dot")
    .data(props.data)
    .enter()
    .append("circle")
    .attr("cx", (d) => x(d.time))
    .attr("cy", (d) => y(d.temperature))
    .attr("r", 2)
    .attr("fill", "teal")

  // Add apparent temperature line
  svg.append("path")
    .datum(props.data)
    .attr("fill", "none")
    .attr("stroke", "purple")
    .attr("stroke-width", 1.5)
    .attr("d", d3.line()
      .x((d) => x(d.time))
      .y((d) => y(d.apparent_temperature))
    )

  // Add apparent temperature the points
  svg.append("g")
    .selectAll("dot")
    .data(props.data)
    .enter()
    .append("circle")
    .attr("cx", (d) => x(d.time))
    .attr("cy", (d) => y(d.apparent_temperature))
    .attr("r", 2)
    .attr("fill", "purple")
})
</script>
<template>
  <v-card color="amber-lighten-4" flat>
    <div id="temperature"></div>
  </v-card>
</template>
