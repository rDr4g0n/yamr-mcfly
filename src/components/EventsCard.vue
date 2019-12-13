<template>
  <div class="events-card">
    <div class="events-card-header">
      <div class="events-card-title">Events</div>
    </div>
    {{ events && events.length }}
  </div>
</template>

<script>
export default {
  name: "events-card",
  props: {
    events: Array
  }
}
</script>

<style scoped>
.events-card {
  background-color: var(--card-bg);
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
</style>
