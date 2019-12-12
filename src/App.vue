<template>
  <div id="app">
    <div class="item-header-wrap" v-if="false">
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

    <div class="revision-timeline-wrap" v-if="false">
      <div class="revision-timeline">
        {{ revisions.length }} Revisions
      </div>
    </div>

    <div class="revision-viewer-wrap">
      <div class="prev-revision">
        <RevisionCard
          v-if="prevRevision"
          title="Previous Revision"
          :timestamp="prevRevision.timestamp"
          :fields="prevRevision.fields"
          :diffFields="selectedRevision.fields"
          :diffOnly="true"
        >
          <template v-slot:actions>
            <div class="action-icon" @click="compareRevision(prevRevision, 'Previous')">#</div>
            <div class="action-icon" @click="selectRevision(prevRevision)">o</div>
          </template>
        </RevisionCard>
        <div class="no-revision-message" v-else>
          <div>No older revisions within this timeframe</div>
        </div>
      </div>
      <div class="selected-revision">
        <RevisionCard
          v-if="selectedRevision"
          :title="selectedRevisionTitle"
          :timestamp="selectedRevision.timestamp"
          :fields="selectedRevision.fields"
          :diffFields="diffRevision ? diffRevision.fields : null"
        >
          <template v-slot:actions v-if="diffRevision">
            <div class="action-icon" @click="compareRevision()">X</div>
          </template>
        </RevisionCard>
      </div>
      <div class="next-revision">
        <RevisionCard
          v-if="nextRevision"
          class="compact-revision"
          title="Next Revision"
          :timestamp="nextRevision.timestamp"
          :fields="nextRevision.fields"
          :diffFields="selectedRevision.fields"
          :diffOnly="true"
        >
          <template v-slot:actions>
            <div class="action-icon" @click="compareRevision(nextRevision, 'Next')">#</div>
            <div class="action-icon" @click="selectRevision(nextRevision)">o</div>
          </template>
        </RevisionCard>
        <div class="no-revision-message" v-else>
          <div>No newer revisions within this timeframe</div>
        </div>
      </div>
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
      prevRevision: null,
      selectedRevision: null,
      nextRevision: null,
      diffRevision: null,
      diffRevisionTitle: null,
    }
  },
  computed: {
    selectedRevisionTitle(){
      if(this.diffRevisionTitle){
        return `Comparing Selected and ${this.diffRevisionTitle} Revision`
      }
      return "Selected Revision"
    }
  },
  methods: {
    selectRevision(revision){
      const i = this.revisions.indexOf(revision)
      this.selectedRevision = this.revisions[i]
      this.prevRevision = i ? this.revisions[i-1] : null
      this.nextRevision = i < this.revisions.length ? this.revisions[i+1] : null
      // reset any comparison junk
      this.compareRevision()
    },
    compareRevision(revision, title){
      this.diffRevision = revision
      this.diffRevisionTitle = title
    }
  },
  mounted(){
    this.selectRevision(this.revisions[this.revisions.length-1])
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

.selected-revision {
  flex: 1;
}
.prev-revision,
.next-revision {
  flex: 0 0 400px;
}

.no-revision-message {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 40px 20px;
  height: 200px;
  color: var(--secondary-text);
  border: solid var(--secondary-text) 1px;
}

.action-icon {
  cursor: pointer;
  padding: 0 5px;
}
.action-icon:hover {
  color: deeppink;
}


</style>
