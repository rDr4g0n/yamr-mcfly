<template>
  <div class="field-renderer">
    <template v-if="isDiff(field)">
      <div v-if="field.from" class="field-from-value">{{ field.from }}</div>
      <div v-if="field.value !== null" class="field-to-value">{{ field.value }}</div>
    </template>
    <template v-else>
      <div class="field-value">{{ field.value }}</div>
    </template>
  </div>
</template>

<script>
export default {
  name: "default-renderer",
  props: {
    field: Object
  },
  methods: {
    isDiff(field){
      return "from" in field
    },
  }
}
</script>

<style scoped>
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
