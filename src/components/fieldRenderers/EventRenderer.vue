<template>
  <div class="event-renderer field-renderer">
    <template v-if="isDiff(field)">
      <div v-if="field.from" class="field-from-value">
        <a
          class="item-link reverse"
          v-for="(value, i) in valueAsArray(field.from)"
          :key="i"
          :href="value.url"
        >{{ value.id }}</a>
      </div>
      <div v-if="hasValue" class="field-to-value">
        <a
          class="item-link reverse"
          v-for="(value, i) in valueAsArray(field.value)"
          :key="i"
          :href="value.url"
        >{{ value.id }}</a>
      </div>
    </template>
    <template v-else>
      <div v-if="hasValue" class="field-value">
        <a
          class="unstyled-link"
          v-for="(value, i) in valueAsArray(field.value)"
          :key="i"
          :href="value.url"
        >
          <div class="event-class">{{ value.eventClass }}</div>
          <div class="event-summary">{{ value.summary }}</div>
        </a>
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: "event-renderer",
  props: {
    field: Object,
  },
  computed: {
    hasValue(){
      return this.field.value !== undefined && this.field.value !== null
    }
  },
  methods: {
    valueAsArray(value){
      if(!Array.isArray(value)){
        return [this.valueObject(value)]
      }
      return value.map(v => this.valueObject(v))
    },
    valueObject(value){
      if(typeof value === "string"){
        return {
          id: value,
          eventClass: "",
          summary: "",
          url: "#"
        }
      }
      return {
        id: value.id,
        eventClass: value.eventClass,
        summary: value.summary,
        url: `/#/${value.type || "E"}/${value.id}`
      }
    },
    isDiff(field){
      return "from" in field
    },
  }
}
</script>

<style scoped>
.item-link {
  color: var(--action);
}
.field-from-value,
.field-to-value,
.field-value {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}
.item-link.reverse {
  color: var(--action-reverse);
}
.item-link:hover {
  color: var(--action-reverse);
}

.unstyle-link {
  display: flex;
}
.event-class {
  color: var(--secondary-text);
  text-decoration: none;
  font-weight: bold;
}
.event-summary {
  color: white;
  text-decoration: none;
}
</style>
