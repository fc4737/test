<template>
  <div class="lc-switch" :class="{ hidden: config.hidden }">
    <label class="lc-switch-label">
      {{ config.label }}
      <span v-if="config.required" class="required">*</span>
    </label>
    <label class="lc-switch-wrapper">
      <input type="checkbox" class="lc-switch-input" />
      <span class="lc-switch-track">
        <span class="lc-switch-thumb"></span>
      </span>
    </label>
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
.lc-switch {
  display: flex;
  align-items: center;
  gap: 12px;
}

.lc-switch-label {
  font-size: 14px;
  color: var(--text-primary);
  font-weight: 500;
}

.required {
  color: var(--error-color);
  margin-left: 4px;
}

.lc-switch-wrapper {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.lc-switch-input {
  display: none;
}

.lc-switch-track {
  position: relative;
  width: 44px;
  height: 24px;
  background: var(--border-color);
  border-radius: 12px;
  transition: background 0.2s;
}

.lc-switch-input:checked + .lc-switch-track {
  background: var(--primary-color);
}

.lc-switch-thumb {
  position: absolute;
  top: 2px;
  left: 2px;
  width: 20px;
  height: 20px;
  background: #fff;
  border-radius: 50%;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s;
}

.lc-switch-input:checked + .lc-switch-track .lc-switch-thumb {
  transform: translateX(20px);
}

.lc-switch.hidden {
  display: none;
}
</style>