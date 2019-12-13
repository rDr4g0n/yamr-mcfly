<template>
  <div class="revision-card card" :class="{ diff: diffOnly, compact: compact }">
    <div class="revision-card-header">
      <div class="revision-card-title">{{ title }}</div>
      <div class="revision-card-actions"><slot name="actions"></slot></div>
    </div>
    <div class="revision-card-datetime">
      <div class="revision-card-date">{{ timestamp | toDate }}</div>
      <div class="revision-card-time">{{ timestamp | toTime }}</div>
    </div>
    <ItemView
      v-if="exists"
      :fields="formattedFields"
      :diffOnly="diffOnly"
      :fieldNamesOnly="compact"
      :fieldsMap="fieldsMap"
    />
    <div v-else>This thing stopped existing. Woah. </div>
  </div>
</template>

<script>
import ItemView from "./ItemView"
import fieldsMap from "./fieldsMap"

const isEqual = (a, b) => JSON.stringify(a) === JSON.stringify(b)
const getValue = value => value.length > 1 ? value : value[0]

const fieldsMapByType = (itemType) => fieldsMap[itemType]

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
    exists: {
      type: Boolean,
      default: true
    },
    reverseDiff: {
      type: Boolean,
      default: false
    },
    diffOnly: {
      type: Boolean,
      default: false
    },
    compact: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    fieldsA(){
      return this.reverseDiff ? this.diffFields : this.fields
    },
    fieldsB(){
      return this.reverseDiff ? this.fields : this.diffFields
    },
    fieldsMap(){
      return fieldsMapByType(this.itemType)
    },
    formattedFields(){
      let fields = Object.entries(this.fieldsA)
        .map(([name, value]) => {
          const f = {
            name,
            value: getValue(value)
          }
          if(this.fieldsB){
            let from = this.fieldsB[name]
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
      if(this.fieldsB){
        // look for diffFields which do NOT exist in
        // this.fields
        Object.entries(this.fieldsB)
          .forEach(([name, value]) => {
            if(!this.fieldsA[name]){
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
.revision-card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: var(--base-margin) calc(var(--base-margin) * 1.5);
  height: 45px;
}
.revision-card-title {
  color: var(--secondary-text);
  font-style: italic;
}
.revision-card-actions {
    display: flex;
}
.revision-card-datetime {
  display: flex;
  align-items: baseline;
  padding: var(--base-margin) calc(var(--base-margin) * 1.5);
}
.revision-card-date {
  font-size: 24px;
  font-weight: bold;
  margin-right: 5px;
}
.revision-card-time {
  color: var(--secondary-text);
  font-size: 18px;
}

.compact .revision-card-datetime {
  padding: 2px 10px;
  flex-direction: column;
}
.compact .revision-card-date {
  font-size: 16px;
}
.compact .revision-card-time {
  font-size: 16px;
}
.compact .revision-card-header {
  padding: 10px;
  padding-bottom: 0;
  height: auto;
}
.compact >>> .item-view {
  padding: 5px 10px;
}
.compact >>> .item-view .field {
  display: inline-block;
  margin: 0;
}
.compact >>> .item-view .field-key {
  padding-right: 4px;
  font-size: 16px;
  color: white;
}
.compact >>> .item-view .field-key:after {
  content: ", ";
}
</style>
