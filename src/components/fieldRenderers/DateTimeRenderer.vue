<template>
  <div class="date-time-renderer field-renderer">
    <template v-if="isDiff(field)">
      <div v-if="field.from" class="field-from-value">
        {{ field.from | toDate }}&nbsp;
        {{ field.from | toTime }}
      </div>
      <div v-if="field.value !== null" class="field-to-value">
        {{ field.value | toDate }}&nbsp;
        {{ field.value | toTime }}
      </div>
    </template>
    <template v-else>
      <div class="field-value">
        {{ field.value | toDate }}&nbsp;
        {{ field.value | toTime }}
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: "date-time-renderer",
  props: {
    field: Object
  },
  methods: {
    isDiff(field){
      return "from" in field
    },
  },
}
</script>
