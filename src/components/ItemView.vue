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
        <component
          :is="componentNameForField(field.name)"
          :field="field"
          :itemType="itemType"
        />
      </div>
     </template>
  </div>
</template>

<script>
import renderers from "./fieldRenderers"

export default {
  name: "item-view",
  components: {
    ...renderers
  },
  props: {
    itemType: String,
    fields: Array,
    fieldsMap: {
      type: Object,
      default(){
        return {}
      }
    },
    diffOnly: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    isDiff(field){
      return "from" in field
    },
    componentNameForField(fieldName){
      return `${(this.fieldsMap[fieldName] || "Default")}Renderer`
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
  overflow-wrap: break-word;
}
.field-key {
  font-size: 12px;
  color: var(--secondary-text);
  padding-bottom: 4px;
}
</style>

<style>
.field-renderer .field-value {
  font-size: 16px;
}
.field-renderer .field-to-value {
  background-color: darkgreen;
}
.field-renderer .field-to-value:before {
  font-family: monospace;
  color: palegreen;
  content: "+";
}
.field-renderer .field-from-value {
  background-color: firebrick;
}
.field-renderer .field-from-value:before {
  font-family: monospace;
  color: lightsalmon;
  content: "-";
}
</style>
