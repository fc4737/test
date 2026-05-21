<template>
  <div class="lc-card" :style="{ width: `${config.width}px` }" :class="{ hidden: config.hidden }">
    <div class="lc-card-header">
      <h3 class="lc-card-title">{{ config.label || '卡片标题' }}</h3>
    </div>
    <div class="lc-card-body">
      <p class="lc-card-content">这是卡片的内容区域，可以放置各种组件。</p>
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
.lc-card {
  border: 1px solid var(--border-color);
  border-radius: 8px;
  overflow: hidden;
  background: #fff;
}

.lc-card-header {
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
  background: #fafafa;
}

.lc-card-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.lc-card-body {
  padding: 16px;
}

.lc-card-content {
  font-size: 14px;
  color: var(--text-secondary);
  margin: 0;
  line-height: 1.6;
}

.lc-card.hidden {
  display: none;
}
</style>