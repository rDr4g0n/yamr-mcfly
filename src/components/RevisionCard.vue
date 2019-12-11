<template>
  <div class="revision-card">
    <div class="revision-card-header">
      <div class="revision-card-title">{{ title }}</div>
    </div>
    <div class="revision-card-datetime">
      <div class="revision-card-date">{{ timestamp | toDate }}</div>
      <div class="revision-card-time">{{ timestamp | toTime }}</div>
    </div>
    <ItemView :item="fields" />
  </div>
</template>

<script>
import ItemView from "./ItemView"

export default {
  name: 'revision-card',
  components: {
    ItemView
  },
  props: {
    title: String,
    timestamp: Number,
    fields: Object,
  }
}

</script>

<style scoped>
.revision-card {
  background-color: white;
  margin: 40px 20px;
}

.revision-card-header {
  display: flex;
  align-items: space-between;
  padding: calc(var(--base-margin) * 1.5);
}
.revision-card-title {
  color: var(--secondary-text);
}
.revision-card-datetime {
  padding: calc(var(--base-margin) * 1.5);
}
.revision-card-date {
  font-size: 24px;
}
.revision-card-time {
  font-size: 18px;
  font-weight: bold;
}
</style>
