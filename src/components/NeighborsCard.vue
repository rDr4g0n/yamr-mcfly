<template>
  <div class="neighbors-card card">
    <div class="neighbors-card-header">
      <div class="neighbors-card-title">Neighbors ({{ upstairsCount + downstairsCount }})</div>
    </div>
    <div class="items-list">
      <div class="neighbor-direction-label">Upstairs ({{ upstairsCount }})</div>
      <a
         v-for="item in formattedParentItems"
         :key="item.id"
         :href="item.url"
      >{{ item.name }}</a><br>
      <div class="neighbor-direction-label">Downstairs ({{ downstairsCount }})</div>
      <a
         v-for="item in formattedChildItems"
         :key="item.id"
         :href="item.url"
      >{{ item.name }}</a>
    </div>
  </div>
</template>

<script>
export default {
  name: "neighbors-card",
  props: {
    neighbors: Object
  },
  computed: {
    upstairsCount(){
      return this.parents.length
    },
    downstairsCount(){
      return this.children.length
    },
    parents(){
      return this.neighbors && this.neighbors.parents ?
        this.neighbors.parents : []
    },
    children(){
      return this.neighbors && this.neighbors.children ?
        this.neighbors.children : []
    },
    formattedParentItems(){
      return this.parents.map(item => ({
        id: item.id,
        name: item.name,
        url: `/#/${item.type}/${item.id}`
      }))
    },
    formattedChildItems(){
      return this.children.map(item => ({
        id: item.id,
        name: item.name,
        url: `/#/${item.type}/${item.id}`
      }))
    }
  }
}
</script>

<style scoped>
.neighbors-card {
  padding: var(--base-margin) calc(var(--base-margin) * 1.5);
}
.neighbors-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 45px;
}
.neighbors-card-title {
  color: var(--secondary-text);
  font-style: italic;
}
.items-list {
  display: flex;
  flex-direction: column;
}

.neighbor-direction-label {
  font-size: 12px;
  color: var(--secondary-text);
  padding-bottom: 4px;
}
</style>
