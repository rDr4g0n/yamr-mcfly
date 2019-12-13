<template>
  <div class="metrics-card">
    <div class="metrics-card-header">
      <div class="metrics-card-title">Metrics ({{ count }}) </div>
    </div>
    <div class="items-list">
      <a
         v-for="item in formattedItems"
         :key="item.id"
         :href="item.url"
      >{{ item.name }}</a>
    </div>
  </div>
</template>

<script>
export default {
  name: "metrics-card",
  props: {
    metrics: Array
  },
  computed: {
    count(){
      return this.metrics ? this.metrics.length : "-"
    },
    formattedItems(){
      const items = this.metrics || []
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
.metrics-card {
  background-color: var(--card-bg);
  padding: var(--base-margin) calc(var(--base-margin) * 1.5);
}
.metrics-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 45px;
}
.metrics-card-title {
  color: var(--secondary-text);
  font-style: italic;
}
.items-list {
  display: flex;
  flex-direction: column;
}
</style>
