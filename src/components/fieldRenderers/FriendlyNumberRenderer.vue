<template>
  <div class="friendly-number-renderer field-renderer">
    <template v-if="isDiff(field)">
      <div v-if="field.from" class="field-from-value">{{ field.from | toFriendlyNumber }}</div>
      <div v-if="field.value !== null" class="field-to-value">{{ field.value | toFriendlyNumber }}</div>
    </template>
    <template v-else>
      <div class="field-value">{{ field.value | toFriendlyNumber }}</div>
    </template>
  </div>
</template>

<script>
import { format } from "d3-format"
export default {
  name: "friendly-number-renderer",
  props: {
    field: Object
  },
  methods: {
    isDiff(field){
      return "from" in field
    },
  },
  filters: {
    toFriendlyNumber(val){
      return format(".6s")(val)
    }
  }
}
</script>
