<template>
  <div id="app">
    <div class="item-header-wrap">
      <div class="item-header">
        <div class="item-name">{{ itemName }}</div>
        <div class="item-type">{{ itemType }}</div>
      </div>
      <div class="time-range">
        <input class="time-range-start" v-model="start">
        <span>to</span>
        <input class="time-range-start" v-model="end">
      </div>
    </div>

    <div class="revision-timeline-wrap">
      <div class="revision-timeline">
        {{ revisions.length }} Revisions
      </div>
    </div>

    <div class="revision-viewer-wrap">
      <RevisionCard
          v-for="(revision, i) in revisions"
          :key="i"
          :title="`Revision ${i}`"
          :timestamp="revision.timestamp"
          :fields="revision.fields"
      />
    </div>
  </div>

</template>

<script>
import Vue from "vue"
import moment from "moment"
import { data } from "./data"
import RevisionCard from "./components/RevisionCard"

Vue.filter("toDate", val => moment(val).format("MMMM Do, YYYY"))
Vue.filter("toTime", val => moment(val).format("h:mm a"))

export default {
  name: 'app',
  components: {
    RevisionCard
  },
  data(){
    return {
      itemType: "E",
      itemId: "1234",
      itemName: "testrail.zenoss.loc",
      start: null,
      end: null,
      revisions: data,
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
  font-family: Helvetica, Arial, sans-serif;
  font-size: 16px;
}

:root {
  --secondary-text: #777;
  --base-margin: 10px;
}

html, body, #app {
  height: 100%;
  overflow-x: hidden;
}

#app {
  display: flex;
  flex-direction: column;
  align-items: stretch;
}

.revision-timeline-wrap,
.item-header-wrap {
  width: 100%;
  max-width: 800px;
  margin: 0 auto 40px auto;
}

.item-header-wrap {
  display: flex;
  justify-content: space-between;
  padding-top: 40px;
}

.revision-timeline-wrap {
  padding-top: 45px;
  text-align: center;
  height: 100px;
  border: solid 1px black;
}

.revision-viewer-wrap {
  flex: 1;
  background-color: #CCC;
  width: 100%;
  display: flex;
  align-items: flex-start;
  overflow-x: auto;
}

</style>
