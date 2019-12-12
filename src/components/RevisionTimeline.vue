<template>
  <div class="revision-timeline">
    <svg class="revision-timeline-svg" ref="svg">
      <line
        class="x-axis"
        :x1="0"
        :x2="w"
        y1="17"
        y2="17"
        stroke="#888"
      />
      <g>
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
      <g ref="tick-marks">
        <line
          class="tick-mark"
          v-for="tick in ticks"
          :key="tick"
          :x1="xScale(tick)"
          :x2="xScale(tick)"
          y1="35"
          y2="40"
          stroke="#CCC"
        />
      </g>
    </svg>
    <div class="start-end-labels">
      <div class="start-label">{{ start | toDate }}</div>
      <div class="end-label">{{ end | toDate }}</div>
    </div>
  </div>
</template>

<script>
import { scaleLinear } from "d3-scale"
/*
  - identify selected revision
  - find all fields which ever have changes and draw a line for each
*/

const PADDING = 20

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
        .range([PADDING, this.w - (PADDING / 2)])
    },
    ticks(){
      return [this.start, this.end]
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
  }
}

</script>

<style scoped>
.revision-timeline-svg {
  width: 100%;
  height: 40px;
}
.revision-mark {
  fill: #555;
  cursor: pointer;
}
.revision-mark.selected {
  fill: white;
}
.revision-mark:hover {
  fill: var(--action);
}
.start-end-labels {
  width: 100%;
  display: flex;
  justify-content: space-between;
  color: var(--secondary-text);
}
.start-label {
  text-align: left;
  font-size: 14px;
}
.end-label {
  text-align: right;
  font-size: 14px;
}
</style>
