<template>
  <div class="loader" v-show="isVisible">
    <div v-if="loadingError" class="loading-error">
      <div>{{ loadingError.message }} :(</div>
      <div class="link" @click="onRetryClick">Retry?</div>
    </div>
    <div v-else-if="value" class="loading-spinner">❤</div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: Boolean,
      default: false
    },
    loadingError: Object
  },
  computed: {
    isVisible(){
      return this.value || this.loadingError
    }
  },
  methods: {
    onRetryClick(){
      this.$emit("retry", this.loadingError.retryArgs)
    }
  }
}
</script>

<style scoped>
.loader {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
  background-color: rgba(0,0,0,0.8);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}

.loading-spinner {
  color: var(--action);
  font-size: 200px;
  animation-duration: 0.5s;
  animation-name: loader;
  animation-iteration-count: infinite;
  animation-timing-function: ease-in-out;
}

@keyframes loader {
  0% {
    opacity: 1;
    transform: scale(1);
  }
  50% {
    opacity: 0.5;
    transform: scale(0.5);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}

.loading-error {
  text-align: center;
}

.link {
  color: var(--action);
  text-decoration: underline;
  cursor: pointer;
}

</style>
