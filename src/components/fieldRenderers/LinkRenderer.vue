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
      <div v-if="field.value !== null" class="field-to-value">
        <a
          class="item-link reverse"
          v-for="(value, i) in valueAsArray(field.value)"
          :key="i"
          :href="value.url"
        >{{ value.name }}</a>
      </div>
    </template>
    <template v-else>
      <div class="field-value">
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
    itemType: {
      type: String,
      required: true
    },
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
          type: this.itemType,
          url: `/${this.itemType}/${value}`
        }
      }
      return Object.assign(
        {},
        value,
        { url: `/${this.itemType}/${value}` }
      )
    },
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

.item-link {
  display: block;
  color: var(--action);
}
.item-link.reverse {
  color: var(--action-reverse);
}
</style>
