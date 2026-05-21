<template>
  <div class="lc-datepicker" :style="{ width: `${config.width}px` }">
    <label v-if="config.label" class="lc-label">
      {{ config.label }}
      <span v-if="config.required" class="required">*</span>
    </label>
    <input 
      type="date" 
      class="lc-datepicker-field"
      :class="{ hidden: config.hidden }"
    />
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
.lc-datepicker {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.lc-label {
  font-size: 14px;
  color: var(--text-primary);
  font-weight: 500;
}

.required {
  color: var(--error-color);
  margin-left: 4px;
}

.lc-datepicker-field {
  width: 100%;
  padding: 10px 12px;
  font-size: 14px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  outline: none;
  transition: all 0.2s;
  background: #fff;
  cursor: pointer;
}

.lc-datepicker-field:focus {
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(64, 128, 255, 0.1);
}

.lc-datepicker-field.hidden {
  display: none;
}
</style>