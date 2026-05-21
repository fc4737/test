<template>
  <div class="lc-button" :style="{ width: `${config.width}px` }">
    <button 
      class="lc-btn" 
      :class="{ hidden: config.hidden }"
    >
      {{ config.label }}
    </button>
  </div>
</template>

<script setup>
defineProps({
  config: {
    type: Object,
    required: true
  },
  preview: {
    type: Boolean,
    default: false
  }
})
</script>

<style scoped>
.lc-button {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.lc-btn {
  width: 100%;
  padding: 10px 16px;
  font-size: 14px;
  font-weight: 500;
  color: #fff;
  background: var(--primary-color);
  border: none;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
}

.lc-btn:hover {
  background: #609fff;
}

.lc-btn:active {
  background: #3070e0;
}

.lc-btn.hidden {
  display: none;
}
</style>