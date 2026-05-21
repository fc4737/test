<template>
  <div class="lc-radio" :style="{ width: `${config.width}px` }" :class="{ hidden: config.hidden }">
    <label v-if="config.label" class="lc-label">
      {{ config.label }}
      <span v-if="config.required" class="required">*</span>
    </label>
    <div class="lc-radio-group">
      <label 
        v-for="(option, index) in (config.options || ['选项1', '选项2', '选项3'])" 
        :key="index" 
        class="lc-radio-item"
      >
        <input type="radio" :name="`radio-${config.label}`" :value="option" />
        <span class="lc-radio-outer">
          <span class="lc-radio-inner"></span>
        </span>
        <span class="lc-radio-text">{{ option }}</span>
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
.lc-radio {
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

.lc-radio-group {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}

.lc-radio-item {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.lc-radio-item input {
  display: none;
}

.lc-radio-outer {
  width: 18px;
  height: 18px;
  border: 2px solid var(--border-color);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.lc-radio-inner {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: transparent;
  transition: background 0.2s;
}

.lc-radio-item input:checked + .lc-radio-outer {
  border-color: var(--primary-color);
}

.lc-radio-item input:checked + .lc-radio-outer .lc-radio-inner {
  background: var(--primary-color);
}

.lc-radio-text {
  font-size: 14px;
  color: var(--text-primary);
}

.lc-radio.hidden {
  display: none;
}
</style>