<template>
  <div class="item-view">
    <div
      class="field"
      v-for="(value, key) in item"
      :key="key"
    >
      <div class="field-key">{{ key }}</div>
      <div class="field-value">{{ value[0] }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "item-view",
  props: {
    item: Object
  },
}
</script>

<style scoped>
.item-view {
  padding: calc(var(--base-margin) * 2);
}
.field {
  margin-bottom: calc(var(--base-margin) * 1.5);
}
.field-key {
  font-size: 12px;
  color: var(--secondary-text);
}
.field-value {
  font-size: 16px;
}
</style>
