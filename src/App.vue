<template>
  <div id="app">
    <div class="loader" v-if="loading"></div>
    <div style="background-color: #111;">
      <div class="item-header-wrap">
        <div class="item-header">
          <div class="item-name">{{ itemName }}</div>
          <div class="item-type">{{ labelForType(itemType) }}</div>
        </div>
        <!--
        <div class="time-range">
          <input class="time-range-start" v-model="start">
          <span>to</span>
          <input class="time-range-start" v-model="end">
        </div>
        -->
      </div>

      <div class="revision-timeline-wrap">
        <RevisionTimeline
          :revisions="revisions"
          :start="start"
          :end="end"
          :selectedRevision="selectedRevision"
          @selectRevision="selectRevision"
        />
      </div>
    </div>

    <div class="revision-viewer-wrap">
      <div class="prev-revision">
        <div class="revision-mark" @click="selectRevision(prevRevision)">◆</div>
        <RevisionCard
          v-if="prevRevision"
          title="Previous Biff"
          :timestamp="prevRevision.timestamp"
          :itemType="itemType"
          :fields="prevRevision.fields"
          :diffFields="selectedRevision.fields"
          :diffOnly="true"
        >
          <template v-slot:actions>
            <div class="action-icon" @click="compareRevision(prevRevision, 'Previous')">±</div>
            <div class="action-icon" @click="selectRevision(prevRevision)">✓</div>
          </template>
        </RevisionCard>
        <div class="no-revision-message" v-else>
          <div>No older revisions within this timeframe</div>
        </div>
      </div>
      <div class="selected-revision">
        <div class="revision-mark selected">◆</div>
        <RevisionCard
          :class="{'is-comparing': diffRevision}"
          v-if="selectedRevision"
          :title="selectedRevisionTitle"
          :timestamp="selectedRevision.timestamp"
          :itemType="itemType"
          :fields="selectedRevision.fields"
          :diffFields="diffRevision ? diffRevision.fields : null"
        >
          <template v-slot:actions v-if="diffRevision">
            <div class="action-icon" @click="compareRevision()">⨯</div>
          </template>
        </RevisionCard>
      </div>
      <div class="next-revision">
        <div class="revision-mark" @click="selectRevision(nextRevision)">◆</div>
        <RevisionCard
          v-if="nextRevision"
          class="compact-revision"
          title="Next Biff"
          :timestamp="nextRevision.timestamp"
          :itemType="itemType"
          :fields="nextRevision.fields"
          :diffFields="selectedRevision.fields"
          :diffOnly="true"
        >
          <template v-slot:actions>
            <div class="action-icon" @click="compareRevision(nextRevision, 'Next')">±</div>
            <div class="action-icon" @click="selectRevision(nextRevision)">✓</div>
          </template>
        </RevisionCard>
        <div class="no-revision-message" v-else>
          <div>No newer revisions within this timeframe</div>
        </div>
      </div>
      <div class="revision-marks-line"></div>
    </div>
  </div>

</template>

<script>
import Vue from "vue"
import moment from "moment"
import RevisionCard from "./components/RevisionCard"
import RevisionTimeline from "./components/RevisionTimeline"

Vue.filter("toDate", val => moment(val).format("MMMM Do, YYYY"))
Vue.filter("toTime", val => moment(val).format("h:mm:ss a"))

export default {
  name: 'app',
  components: {
    RevisionCard,
    RevisionTimeline,
  },
  data(){
    return {
      itemType: null,
      itemId: null,
      itemName: null,
      start: null,
      end: null,
      revisions: null,
      prevRevision: null,
      selectedRevision: null,
      nextRevision: null,
      diffRevision: null,
      diffRevisionTitle: null,
      loading: false,
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
    },
    labelForType(type){
      return {
        "E": "Entity",
        "M": "Metric",
        "V": "Event",
      }[type]
    },
    async fetchRevisions(){
      if(!this.itemType || !this.itemId){
        console.warn("didnt try to fetch", this.itemType, this.itemId)
        return
      }
      this.loading = true
      console.log("fetching revisions", this.itemType, this.itemId)
      const url = `http://10.87.111.197:8089/${this.itemType}/${this.itemId}`
      const resp = await fetch(url)
      const data = await resp.json()
      data.sort((a, b) => a.timestamp - b.timestamp)

      // TODO - handle err, empty results, etc
      this.revisions = data
      // TODO - check number of revisions
      this.selectRevision(this.revisions[this.revisions.length-1])

      // TODO - be much safer here
      const { fields, timestamp } = this.selectedRevision
      this.itemName = fields.name ? fields.name[0] : "[ Unnamed ]"
      this.end = timestamp
      this.start = this.revisions[0].timestamp

      this.loading = false
    },
    evaluateHash(){
      const parts = window.location.hash.split("/")
      this.itemType = parts[1]
      this.itemId = parts[2]
    },
  },
  created(){
    this.evaluateHash()
    window.addEventListener("hashchange", (newUrl, oldUrl) => {
      if(newUrl !== oldUrl){
        this.evaluateHash()
      }
    })
    document.addEventListener("keyup", e => {
      const { code } = e
      if(code === "ArrowLeft" && this.prevRevision){
        this.selectRevision(this.prevRevision)
      } else if(code === "ArrowRight" && this.nextRevision){
        this.selectRevision(this.nextRevision)
      }
    })
  },
  watch: {
    itemId: {
      immediate: true,
      handler(){
        this.fetchRevisions()
      }
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
  --primary-text: #DDD;
  --secondary-text: #999;
  --base-margin: 10px;
  --action: cyan;
  --action-reverse: white;
}

html, body, #app {
  min-height: 100vh;
}

#app {
  color: var(--primary-text);
  display: flex;
  flex-direction: column;
  align-items: stretch;
  background-color: #444;
  width: 100%;
  overflow: hidden;
}

.loader {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  background-color: rgba(0,0,0,0.8);
  z-index: 10;
}

.revision-timeline-wrap,
.item-header-wrap {
  width: 100%;
  max-width: 800px;
  margin: 0 auto 20px auto;
}

.item-header-wrap {
  display: flex;
  justify-content: space-between;
  padding-top: 20px;
}

.item-header {
  display: flex;
  align-items: baseline;
}
.item-name {
  font-size: 24px;
  font-weight: bold;
  margin-right: 5px;
}
.item-type {
  color: var(--secondary-text);
  font-size: 18px;
}

.revision-timeline-wrap {
  height: 30px;
}

.revision-viewer-wrap {
  position: relative;
  flex: 1;
  width: 100%;
  display: flex;
  align-items: flex-start;
}

.revision-marks-line {
  position: absolute;
  top: 25px;
  left: 0;
  width: 100%;
  height: 1px;
  border-bottom: solid 2px #555;
}

.revision-viewer-wrap .revision-mark {
  position: relative;
  margin: 10px 0;
  z-index: 2;
  font-size: 24px;
  color: #555;
  cursor: pointer;
}
.revision-viewer-wrap .revision-mark.selected {
  color: white;
}
.revision-viewer-wrap .revision-mark:hover {
  color: var(--action);
}

.revision-card.is-comparing .revision-card-header {
  background-color: black;
}

.selected-revision,
.prev-revision,
.next-revision {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 0 10px 10px 10px;
}
.selected-revision {
  flex: 1;
  overflow: hidden;
}
.prev-revision,
.next-revision {
  flex: 0 1 400px;
  max-width: 400px;
}
.prev-revision .revision-card-date,
.next-revision .revision-card-date {
  font-size: 18px;
  font-weight: normal;
}

.no-revision-message {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  padding: 10px;
  height: 200px;
  color: var(--secondary-text);
  border: solid var(--secondary-text) 1px;
}

.action-icon {
  cursor: pointer;
  padding: 5px;
  width: 25px;
  height: 25px;
  text-align: center;
  font-weight: bold;
  font-size: 18px;
}
.action-icon:hover {
  color: var(--action);
}


</style>
