<template>
  <div class="item-view">
    <template
      v-for="field in fields"
    >
      <div
        class="field"
        :key="field.name"
        v-if="!(diffOnly && !isDiff(field))"
      >
        <div class="field-key">{{ field.name }}</div>
        <template v-if="isDiff(field)">
          <div v-if="field.from" class="field-from-value">{{ field.from }}</div>
          <div class="field-to-value">{{ field.value }}</div>
        </template>
        <template v-else>
          <div class="field-value">{{ field.value }}</div>
        </template>
      </div>
     </template>
  </div>
</template>

<script>
export default {
  name: "item-view",
  props: {
    fields: Array,
    diffOnly: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    isDiff(field){
      return "from" in field
    }
  }
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
  padding-bottom: 4px;
}
.field-value {
  font-size: 16px;
}
.field-to-value {
  background-color: darkgreen;
}
.field-to-value:before {
  font-family: monospace;
  color: palegreen;
  content: "+";
}
.field-from-value {
  background-color: firebrick;
}
.field-from-value:before {
  font-family: monospace;
  color: lightsalmon;
  content: "-";
}
</style>
