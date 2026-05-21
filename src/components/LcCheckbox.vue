<template>
  <div class="lc-checkbox" :style="{ width: `${config.width}px` }" :class="{ hidden: config.hidden }">
    <label v-if="config.label" class="lc-label">
      {{ config.label }}
      <span v-if="config.required" class="required">*</span>
    </label>
    <div class="lc-checkbox-group">
      <label 
        v-for="(option, index) in (config.options || ['选项1', '选项2', '选项3'])" 
        :key="index" 
        class="lc-checkbox-item"
      >
        <input type="checkbox" :value="option" />
        <span class="lc-checkbox-box">
          <span class="lc-checkbox-check">✓</span>
        </span>
        <span class="lc-checkbox-text">{{ option }}</span>
      </label>
    </div>
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
.lc-checkbox {
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

.lc-checkbox-group {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}

.lc-checkbox-item {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.lc-checkbox-item input {
  display: none;
}

.lc-checkbox-box {
  width: 18px;
  height: 18px;
  border: 2px solid var(--border-color);
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.lc-checkbox-check {
  font-size: 12px;
  color: #fff;
  opacity: 0;
  transition: opacity 0.2s;
}

.lc-checkbox-item input:checked + .lc-checkbox-box {
  background: var(--primary-color);
  border-color: var(--primary-color);
}

.lc-checkbox-item input:checked + .lc-checkbox-box .lc-checkbox-check {
  opacity: 1;
}

.lc-checkbox-text {
  font-size: 14px;
  color: var(--text-primary);
}

.lc-checkbox.hidden {
  display: none;
}
</style>