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
const getValue = value => value.length > 1 ? value : value[0]

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
      // TODO - sort
      // TODO - record deleted field diffs
      return Object.entries(this.fields)
        .map(([name, value]) => {
          const f = {
            name,
            value: getValue(value)
          }
          if(this.diffFields){
            let from = this.diffFields[name]
            if(from) {
              from = getValue(from)
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
  background-color: #222;
  margin: 20px 10px;
  box-shadow: 4px 4px 20px rgba(0, 0, 0, 0.5);
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
