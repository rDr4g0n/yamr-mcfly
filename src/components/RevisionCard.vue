<template>
  <div class="revision-card" :class="{ diff: diffOnly }">
    <div class="revision-card-header">
      <div class="revision-card-title">{{ title }}</div>
      <div class="revision-card-actions"><slot name="actions"></slot></div>
    </div>
    <div class="revision-card-datetime">
      <div class="revision-card-date">{{ timestamp | toDate }}</div>
      <div class="revision-card-time">{{ timestamp | toTime }}</div>
    </div>
    <ItemView
      :itemType="itemType"
      :fields="formattedFields"
      :diffOnly="diffOnly"
      :fieldsMap="fieldsMap"
    />
  </div>
</template>

<script>
import ItemView from "./ItemView"

const isEqual = (a, b) => JSON.stringify(a) === JSON.stringify(b)
const getValue = value => value.length > 1 ? value : value[0]

const fieldsMapByType = (itemType) => ({
  E: {
    "_parents_": "Link",
    "mem_capacity": "FriendlyNumber"
  }
}[itemType])

export default {
  name: 'revision-card',
  components: {
    ItemView
  },
  props: {
    title: String,
    timestamp: Number,
    itemType: String,
    fields: Object,
    diffFields: Object,
    diffOnly: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    fieldsMap(){
      return fieldsMapByType(this.itemType)
    },
    formattedFields(){
      let fields = Object.entries(this.fields)
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
      // capture removed fields
      if(this.diffFields){
        // look for diffFields which do NOT exist in
        // this.fields
        Object.entries(this.diffFields)
          .forEach(([name, value]) => {
            if(!this.fields[name]){
              fields.push({
                name,
                value: null,
                from: getValue(value),
              })
            }
          })
      }
      return fields.sort((a, b) => a.name.localeCompare(b.name))
    }
  }
}

</script>

<style scoped>
.revision-card {
  background-color: #222;
  box-shadow: 4px 4px 20px rgba(0, 0, 0, 0.5);
  width: 100%;
}
.revision-card.diff {
  background-color: #333;
  box-shadow: none;
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
