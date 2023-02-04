<template>
  <div>
    <div id="svg_container">
      <svg viewBox="0 0 100 100">
          <rect
            v-for="(item, index) in dataset"
            :key="index"
            :x="index * 20"
            :y="100 - item[0]"
            :height="item[0]"
            ref="rects"
            width="10"
            fill="white"
            @click="onClick(item, index)"
          />
        </svg>  
    </div>
  </div>
</template>

<script>
  import '../../assets/css/chart.css'
  import dataset from '../data/dataset.json'
  import * as d3 from 'd3'

  export default {
    name: 'Chart',
    data() {
      return {
        dataset
      }
    },
    methods: {
      onClick(item, index) {
        let el = d3.select(this.$refs.rects[index])
        let currentHeight = el.attr('height')
        let toggleHeight = currentHeight == item[0] ? item[1] : item[0]
        let newColor = ''
        if (index == 0) {
          newColor = '#E04848'
        } else if (index == 1) {
          newColor = '#FDA461'
        }  else if (index == 2) {
          newColor = '#FFF8C9'
        }  else if (index == 3) {
          newColor = '#A6D96A'
        }  else {
          newColor = '#42A85D'
        } 
        let toggleColor = currentHeight == item[0] ? newColor : 'white'

        el.transition()
          .duration(500)
          .ease(d3.easeSinIn)
          .attr('height', toggleHeight)
          .attr('y', 100 - toggleHeight)
          .attr('fill', toggleColor)
      }
    }
  }
</script>

<style>
</style>
