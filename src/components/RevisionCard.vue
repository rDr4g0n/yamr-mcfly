<template>
  <div class="revision-card">
    <div class="revision-card-header">
      <div class="revision-card-title">{{ title }}</div>
      <div class="revision-card-actions"><slot name="actions"></slot></div>
    </div>
    <div class="revision-card-datetime">
      <div class="revision-card-date">{{ timestamp | toDate }}</div>
      <div class="revision-card-time">{{ timestamp | toTime }}</div>
    </div>
    <ItemView :fields="formattedFields" :diffOnly="diffOnly"/>
  </div>
</template>

<script>
import ItemView from "./ItemView"

const isEqual = (a, b) => JSON.stringify(a) === JSON.stringify(b)

export default {
  name: 'revision-card',
  components: {
    ItemView
  },
  props: {
    title: String,
    timestamp: Number,
    fields: Object,
    diffFields: Object,
    diffOnly: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    formattedFields(){
      return Object.entries(this.fields)
        .map(([name, value]) => {
          const f = {
            name,
            // TODO - some vals might be more than just an array
            value: value.length > 1 ? value : value[0],
          }
          if(this.diffFields){
            let from = this.diffFields[name]
            if(from) {
              // TODO - some vals might be more than just an array
              from = from.length > 1 ? from : from[0]
            }
            if(!isEqual(from, f.value)){
              f.from = from
            }
          }
          return f
        })
    }
  }
}

</script>

<style scoped>
.revision-card {
  background-color: white;
  margin: 40px 20px;
}

.revision-card-header {
  display: flex;
  justify-content: space-between;
  padding: calc(var(--base-margin) * 1.5);
}
.revision-card-title {
  color: var(--secondary-text);
}
.revision-card-actions {
    display: flex;
}
.revision-card-datetime {
  padding: calc(var(--base-margin) * 1.5);
}
.revision-card-date {
  font-size: 24px;
}
.revision-card-time {
  font-size: 18px;
  font-weight: bold;
}
</style>
