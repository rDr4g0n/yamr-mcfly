<template>
  <div class="metrics-card">
    <div class="metrics-card-header">
      <div class="metrics-card-title">Metrics</div>
    </div>
    {{ metrics && metrics.length }}
  </div>
</template>

<script>
export default {
  name: "metrics-card",
  props: {
    metrics: Array
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
</style>
