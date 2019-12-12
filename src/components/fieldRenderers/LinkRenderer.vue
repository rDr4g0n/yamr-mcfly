<template>
  <div class="link-renderer field-renderer">
    <template v-if="isDiff(field)">
      <div v-if="field.from" class="field-from-value">
        <a
          class="item-link reverse"
          v-for="(value, i) in valueAsArray(field.from)"
          :key="i"
          :href="value.url"
        >{{ value.name }}</a>
      </div>
      <div v-if="hasValue" class="field-to-value">
        <a
          class="item-link reverse"
          v-for="(value, i) in valueAsArray(field.value)"
          :key="i"
          :href="value.url"
        >{{ value.name }}</a>
      </div>
    </template>
    <template v-else>
      <div v-if="hasValue" class="field-value">
        <a
          class="item-link"
          v-for="(value, i) in valueAsArray(field.value)"
          :key="i"
          :href="value.url"
        >{{ value.name }}</a>
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: "link-renderer",
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
          name: value,
          url: "#"
        }
      }
      return {
        id: value.id,
        name: value.name,
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
</style>
