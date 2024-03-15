<script setup lang="ts">
import { onMounted, computed } from 'vue'
import * as d3 from 'd3'

const props = defineProps({
  data: {
    type: Array,
    required: true,
    default: () => [
      {
        time: new Date(),
        precipitation: 0,
        rain: 0,
        snowfall: 0,
        showers:0,
        precipitation_probability: 0
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


const rainData = computed(() => props.data.map(d => d.rain))
const snowfallData = computed(() => props.data.map(d => d.snowfall))
const precipitationData = computed(() => props.data.map(d => d.precipitation))
const showersData = computed(() => props.data.map(d => d.showers))
const allPrecipitation = computed(() => [...rainData.value, ...snowfallData.value, ...precipitationData.value, ...showersData.value])


onMounted(() => {
  const container = d3.select("#precipitation")
  // Declare the x (horizontal position) scale.
  // const x = d3.scaleUtc()
  //   .domain([new Date(props.data[0].time), new Date(props.data[props.data.length - 1].time)])
  //   .range([marginLeft, width - marginRight])


  // // Declare the y (vertical position) scale.
  // const y = d3.scaleLinear()
  //   .domain([d3.min(allPrecipitation.value), d3.max(allPrecipitation.value)])
  //   .range([height - marginBottom, marginTop])

  // Create the SVG container.
  const svg = container.append("svg")
    .attr("width", width)
    .attr("height", height);
  // X axis: scale and draw:
  const x = d3.scaleLinear()
    .domain([0, 1000])     // can use this instead of 1000 to have the max of data: d3.max(data, function(d) { return +d.price })
    .range([marginLeft, width - marginRight]);
    svg.append("g")
    .attr("transform", `translate(0,${height - marginBottom})`)
    .call(d3.axisBottom(x));

  // set the parameters for the histogram
  const histogram = d3.histogram()
    .value(function (d) { return d.rain; })   // I need to give the vector of value
    .domain(x.domain())  // then the domain of the graphic
    .thresholds(x.ticks(70)); // then the numbers of bins

  // And apply this function to data to get the bins
  const bins = histogram(props.data);

  // Y axis: scale and draw:
  const y = d3.scaleLinear()
    .range([height - marginBottom, marginTop]);
    y.domain([0, d3.max(bins, function (d) { return d.length; })]);   // d3.hist has to be called before the Y axis obviously
    svg.append("g")
    .attr("transform", `translate(${marginLeft}, 0)`)
    .call(d3.axisLeft(y));

  // append the bar rectangles to the svg element
  svg.selectAll("rect")
    .data(bins)
    .join("rect")
    .attr("x", 1)
    .attr("transform", function (d) { return `translate(${x(d.x0)} , ${y(d.length) -30})` })
    .attr("width", function (d) { return x(d.x1) - x(d.x0) - 1;})
    .attr("height", function (d) { return height - y(d.length); })
    .style("fill", "#69b3a2")

})
</script>
<template>
  <v-card color="amber-lighten-4" flat>
    <div id="precipitation"></div>
  </v-card>
</template>
