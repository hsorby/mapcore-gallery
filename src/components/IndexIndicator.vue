<template>
  <div class="indicator-container">
    <div
      v-for="(number, index) in count"
      :key="'indicator_' + number"
      :class="['indicator', { active: current === index }]"
      @click="$emit('clicked', index)"
    />
  </div>
</template>

<script>
export default {
  name: 'IndexIndicator',
  props: {
    count: {
      type: Number,
      default: 0,
    },
    current: {
      type: Number,
      default: 0,
    },
  },
}
</script>

<style scoped>
.indicator-container {
  display: flex;
  justify-content: center;
}
.indicator {
  width: 1rem;
  height: 1rem;
  border-radius: 50%;
  background-color: #e4e7ed;
  margin-left: 0.25rem;
  margin-right: 0.25rem;
}

.indicator.active {
  background-color: #8300bf;
}
</style>
