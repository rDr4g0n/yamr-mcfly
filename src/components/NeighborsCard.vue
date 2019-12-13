<template>
  <div class="neighbors-card card">
    <div class="neighbors-card-header">
      <div class="neighbors-card-title">Neighbors</div>
    </div>
    <div class="items-list">
      <div class="neighbor-direction-label">Upstairs</div>
      <a
         v-for="item in formattedParentItems"
         :key="item.id"
         :href="item.url"
      >{{ item.name }}</a><br>
      <div class="neighbor-direction-label">Downstairs</div>
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
    neighbors: Array
  },
  computed: {
    formattedParentItems(){
      const items = this.neighbors ? this.neighbors.parents : []
      return items.map(item => ({
        id: item.id,
        name: item.name,
        url: `/#/${item.type}/${item.id}`
      }))
    },
    formattedChildItems(){
      const items = this.neighbors ? this.neighbors.children : []
      return items.map(item => ({
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
