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
import DefaultRenderer from "./fieldRenderers/DefaultRenderer"
import LinkRenderer from "./fieldRenderers/LinkRenderer"

export default {
  name: "item-view",
  components: {
    DefaultRenderer,
    LinkRenderer,
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
      return this.fieldsMap[fieldName] || "DefaultRenderer"
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
</style>
