<template>
  <div class="revision-timeline">
    <svg class="revision-timeline-svg">
      <g ref="revision-marks">
        <rect
          class="revision-mark"
          v-for="(revision, i) in revisions"
          :key="i"
          x="0"
          y="10"
          width="15"
          height="15"
          :style="{ transform: `translate(${xScale(revision.timestamp)}px, 0) rotate(45deg)` }"
          @click="onRevisionMarkSelect(revision)"
        />
        <rect
          v-if="selectedRevision"
          class="revision-mark selected"
          x="0"
          y="10"
          width="15"
          height="15"
          :style="{ transform: `translate(${xScale(selectedRevision.timestamp)}px, 0) rotate(45deg)` }"
          @click="onRevisionMarkSelect(selectedRevision)"
        />
      </g>
    </svg>
  </div>
</template>

<script>
import { scaleLinear } from "d3-scale"
/*
  - identify selected revision
  - find all fields which ever have changes and draw a line for each
*/

const PADDING = 15

export default {
  props: {
    start: Number,
    end: Number,
    revisions: Array,
    selectedRevision: Object
  },
  data(){
    return {
      w: 0,
      h: 0,
    }
  },
  computed: {
    xScale(){
      return scaleLinear()
        .domain([this.start, this.end])
        .range([PADDING, this.w - PADDING])
    },
  },
  methods: {
    onRevisionMarkSelect(revision){
      this.$emit("selectRevision", revision)
    },
  },
  mounted(){
    const bb = this.$el.getBoundingClientRect()
    this.w = bb.width
    this.h = bb.height
    console.log(this.w, this.start, this.end)
  }
}

</script>

<style scoped>
.revision-timeline-svg {
  width: 100%;
  height: 100%;
}
.revision-mark {
  fill: #555;
}
.revision-mark.selected {
  fill: white;
}
</style>
