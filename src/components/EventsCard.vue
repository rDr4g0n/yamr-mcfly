<template>
  <div class="events-card card">
    <div class="events-card-header">
      <div class="events-card-title">Critical Events ({{ count }})</div>
    </div>
    <div class="items-list">
      <a
         v-for="item in formattedItems"
         :key="item.id"
         :href="item.url"
      >
        <div class="event-class">{{ item.eventClass }}</div>
        <div class="event-summary">{{ item.summary }}</div>
      </a>
    </div>
  </div>
</template>

<script>
export default {
  name: "events-card",
  props: {
    events: Array
  },
  computed: {
    count(){
      return this.events ? this.events.length : "-"
    },
    formattedItems(){
      const items = this.events || []
      return items.map(item => ({
        id: item.id,
        summary: item.summary,
        eventClass: item.eventClass,
        url: `/#/${item.type}/${item.id}`
      }))
    }
  }
}
</script>

<style scoped>
.events-card {
  padding: var(--base-margin) calc(var(--base-margin) * 1.5);
}
.events-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 45px;
}
.events-card-title {
  color: var(--secondary-text);
  font-style: italic;
}
.items-list {
  display: flex;
  flex-direction: column;
}

.event-class {
  font-weight: bold;
}
.event-summary {
  color: #CCC;
  padding-bottom: 10px;
}
</style>
