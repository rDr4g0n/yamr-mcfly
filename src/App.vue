<template>
  <div id="app">
    <Loader
      :value="loading"
      :loadingError="loadingError"
      @retry="onRetry"
    />
    <div style="background-color: #111;">
      <div class="item-header-wrap">
        <div class="item-header">
          <div class="item-name">{{ itemName }}</div>
          <div class="item-type">{{ labelForType(itemType) }}</div>
        </div>
        <div class="time-range">
          <select class="time-range-duration" v-model="duration">
            <option :value="1000 * 60 * 60 * 24">Last 24 Hours</option>
            <option :value="1000 * 60 * 60 * 24 * 7">Last 7 Days</option>
            <option :value="1000 * 60 * 60 * 24 * 30">Last 30 Days</option>
            <option :value="1000 * 60 * 60 * 24 * 60">Last 60 Days</option>
            <option :value="1000 * 60 * 60 * 24 * 90">Last 90 Days</option>
          </select>
        </div>
      </div>

      <div class="revision-timeline-wrap">
        <div class="revision-timeline-title">Revisions ({{ revisions.length }})</div>
        <RevisionTimeline
          :revisions="revisions"
          :start="start"
          :end="end"
          :selectedRevision="selectedRevision"
          @selectRevision="selectRevision"
        />
      </div>

      <div class="revision-nearby-wrap">
        <div class="prev-revision" @click="selectRevision(prevRevision)">
          <RevisionCard
            v-if="prevRevision"
            title="Previous Biff"
            :timestamp="prevRevision.timestamp"
            :itemType="itemType"
            :fields="prevRevision.fields"
            :diffFields="selectedRevision.fields"
            :diffOnly="true"
            :compact="true"
          />
          <div class="no-revision-message compact" v-else>
            <div>No older revisions within this timeframe</div>
          </div>
        </div>
        <div class="selected-revision compact">
          <RevisionCard
            v-if="selectedRevision"
            :title="selectedRevisionTitle"
            :timestamp="selectedRevision.timestamp"
            :itemType="itemType"
            :fields="selectedRevision.fields"
            :diffFields="selectedRevision.fields"
            :reverseDiff="!!diffRevision"
            :compact="true"
          />
        </div>
        <div class="next-revision" @click="selectRevision(nextRevision)">
          <RevisionCard
            v-if="nextRevision"
            class="compact-revision"
            title="Next Biff"
            :timestamp="nextRevision.timestamp"
            :itemType="itemType"
            :fields="nextRevision.fields"
            :diffFields="selectedRevision.fields"
            :diffOnly="true"
            :compact="true"
          />
          <div class="no-revision-message compact" v-else>
            <div>No newer revisions within this timeframe</div>
          </div>
        </div>
      </div>
    </div>

    <div class="revision-viewer-wrap" v-if="selectedRevision">
      <div class="selected-revision">
        <RevisionCard
          :title="selectedRevisionTitle"
          :timestamp="selectedRevision.timestamp"
          :itemType="itemType"
          :fields="selectedRevision.fields"
          :diffFields="diffRevision ? diffRevision.fields : null"
          :reverseDiff="!!diffRevision"
        />
      </div>
      <div class="additional-revision-tiles">
        <NeighborsCard :neighbors="selectedRevision.neighbors" />
        <EventsCard :events="selectedRevision.events" />
        <MetricsCard :metrics="selectedRevision.metrics" />
      </div>
    </div>
  </div>

</template>

<script>
import Vue from "vue"
import moment from "moment"
import RevisionCard from "./components/RevisionCard"
import RevisionTimeline from "./components/RevisionTimeline"
import EventsCard from "./components/EventsCard"
import MetricsCard from "./components/MetricsCard"
import NeighborsCard from "./components/NeighborsCard"
import Loader from "./components/Loader"

Vue.filter("toDate", val => moment(val).format("MMMM Do, YYYY"))
Vue.filter("toTime", val => moment(val).format("h:mm:ss a"))

const REVISIONS_SERVICE_URL = "http://10.87.111.197:8089"

export default {
  name: 'app',
  components: {
    RevisionCard,
    RevisionTimeline,
    Loader,
    EventsCard,
    MetricsCard,
    NeighborsCard,
  },
  data(){
    return {
      itemType: null,
      itemId: null,
      itemName: null,
      start: null,
      end: null,
      revisions: [],
      prevRevision: null,
      selectedRevision: null,
      nextRevision: null,
      diffRevision: null,
      loading: false,
      loadingError: null,
      duration: 1000 * 60 * 60 * 24,
    }
  },
  computed: {
    selectedRevisionTitle(){
      return this.diffRevisionTitle || "Selected Revision"
    }
  },
  methods: {
    selectRevision(revision){
      if(!revision){
        this.selectedRevision = null
        this.prevRevision = null
        this.nextRevision = null
      }
      const i = this.revisions.indexOf(revision)
      console.log("selecting revision", revision, "at index", i)
      this.selectedRevision = this.revisions[i]
      this.prevRevision = i ? this.revisions[i-1] : null
      this.nextRevision = i < this.revisions.length ? this.revisions[i+1] : null

      if(this.prevRevision){
        // automatically diff against prev
        const title = `Selected Revision (Biffed Against Previous Revision)`
        this.compareRevision(this.prevRevision, title)

      } else {
        // reset any comparison junk
        this.compareRevision()
      }
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
    async fetchRevisions(itemType, itemId){
      if(!itemType || !itemId){
        console.warn("didnt try to fetch", itemType, itemId)
        return
      }
      this.loading = true
      this.loadingError = null
      try {
        this.end = Date.now()
        this.start = Date.now() - this.duration
        console.log("fetching revisions", itemType, itemId)
        const url = `${REVISIONS_SERVICE_URL}/${itemType}/${itemId}?start=${this.start}`
        const resp = await fetch(url)
        const data = await resp.json()
        data.sort((a, b) => a.timestamp - b.timestamp)
        this.revisions = data || []
        this.itemType = itemType
        this.itemId = itemId
      } catch (e) {
        this.loadingError = {
          message: `Failed to load ${itemType}/${itemId}`,
          retryArgs: [itemType, itemId],
        }
        console.error(e)
        return
      } finally {
        this.loading = false
      }

      if(!this.revisions.length){
        this.selectRevision()
        this.itemName = this.itemId
        return
      }

      // TODO - check number of revisions
      this.selectRevision(this.revisions[this.revisions.length-1])
      const { fields } = this.selectedRevision
      this.itemName = fields.name ? fields.name[0] : "[ Unnamed ]"
    },
    evaluateHash(){
      const parts = window.location.hash.split("/")
      const [ _, itemType, itemId ] = parts
      if(itemType !== this.itemType || itemId !== this.itemId){
        this.fetchRevisions(itemType, itemId)
      }
    },
    onRetry(args){
      const [itemType, itemId] = args
      this.fetchRevisions(itemType, itemId)
    }
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
    duration: {
      immediate: true,
      handler(){
        this.fetchRevisions(this.itemType, this.itemId)
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
  --card-bg: #222;
}

html, body, #app {
  min-height: 100vh;
}

a {
  color: var(--action);
}
a:hover {
  color: var(--action-reverse);
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

.revision-timeline-wrap,
.item-header-wrap,
.review-viewer-wrap,
.revision-nearby-wrap {
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
  display: flex;
  flex-direction: column;
}

.revision-timeline {
  flex: 1;
}

.revision-viewer-wrap {
  padding: 20px;
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
.no-revision-message.compact {
  height: 50px;
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

.selected-revision.compact .item-view {
  display: none;
}

.revision-nearby-wrap {
  align-items: stretch;
}
.revision-nearby-wrap {
  position: relative;
  flex: 1;
  display: flex;
  align-items: stretch;
}
.revision-nearby-wrap .prev-revision,
.revision-nearby-wrap .next-revision {
  flex: 1;
}

.additional-revision-tiles {
  flex: 0 0 400px;
  overflow: hidden;
  padding-left: 10px;
}
.additional-revision-tiles > * {
  margin-bottom: 20px;
  width: 100%;
}

.additional-revision-tiles .items-list {
  max-height: 500px;
  overflow-x: none;
  overflow-y: auto;
}

.card {
  background-color: var(--card-bg);
  box-shadow: 4px 4px 20px rgba(0, 0, 0, 0.5);
  width: 100%;
  height: 100%;
}
</style>
