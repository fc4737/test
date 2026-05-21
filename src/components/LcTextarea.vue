<template>
  <div class="lc-textarea" :style="{ width: `${config.width}px` }">
    <label v-if="config.label" class="lc-label">
      {{ config.label }}
      <span v-if="config.required" class="required">*</span>
    </label>
    <textarea 
      :placeholder="config.placeholder || '请输入'"
      class="lc-textarea-field"
      :class="{ hidden: config.hidden }"
    ></textarea>
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
.lc-textarea {
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

.lc-textarea-field {
  width: 100%;
  padding: 10px 12px;
  font-size: 14px;
  border: 1px solid var(--border-color);
  border-radius: 6px;
  outline: none;
  transition: all 0.2s;
  resize: vertical;
  min-height: 100px;
}

.lc-textarea-field:focus {
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(64, 128, 255, 0.1);
}

.lc-textarea-field::placeholder {
  color: var(--text-placeholder);
}

.lc-textarea-field.hidden {
  display: none;
}
</style>