<template>
  <div class="neighbors-card">
    <div class="neighbors-card-header">
      <div class="neighbors-card-title">Neighbors</div>
    </div>
    {{ neighbors && neighbors.length }}
  </div>
</template>

<script>
export default {
  name: "neighbors-card",
  props: {
    neighbors: Array
  }
}
</script>

<style scoped>
.neighbors-card {
  background-color: var(--card-bg);
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
</style>
