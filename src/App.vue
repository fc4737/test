<template>
  <div class="app-container">
    <header class="header">
      <div class="header-left">
        <div class="logo">
          <span class="logo-icon">⚡</span>
          <span class="logo-text">低代码平台</span>
        </div>
      </div>
      <div class="header-center">
        <nav class="nav">
          <button 
            v-for="item in navItems" 
            :key="item.key"
            class="nav-item"
            :class="{ active: activeNav === item.key }"
            @click="activeNav = item.key"
          >
            {{ item.label }}
          </button>
        </nav>
      </div>
      <div class="header-right">
        <button class="btn btn-primary" @click="exportSchema">导出配置</button>
      </div>
    </header>
    
    <main class="main-content">
      <aside class="sidebar">
        <div class="sidebar-header">
          <span class="sidebar-title">组件库</span>
        </div>
        <div class="component-list">
          <div 
            v-for="component in componentList" 
            :key="component.type"
            class="component-item"
            draggable="true"
            @dragstart="handleDragStart($event, component)"
            @dragend="handleDragEnd"
          >
            <span class="component-icon">{{ component.icon }}</span>
            <span class="component-name">{{ component.name }}</span>
          </div>
        </div>
      </aside>
      
      <div class="editor-area">
        <div class="editor-header">
          <span class="editor-title">画布</span>
          <div class="editor-actions">
            <button class="btn btn-outline" @click="clearCanvas">清空画布</button>
            <button class="btn btn-outline" @click="previewMode = !previewMode">
              {{ previewMode ? '编辑模式' : '预览模式' }}
            </button>
          </div>
        </div>
        
        <div 
          class="canvas"
          @dragover.prevent
          @drop="handleDrop"
          @click="handleCanvasClick"
        >
          <div 
            v-for="(item, index) in canvasItems" 
            :key="item.id"
            class="canvas-item"
            :class="{ selected: selectedId === item.id }"
            @click.stop="selectItem(index)"
          >
            <component 
              :is="getComponent(item.type)" 
              :config="item.config"
              :preview="previewMode"
            />
          </div>
          
          <div v-if="canvasItems.length === 0" class="empty-canvas">
            <span class="empty-icon">📦</span>
            <span class="empty-text">从左侧拖拽组件到此处</span>
          </div>
        </div>
      </div>
      
      <aside class="properties-panel" v-if="selectedItem">
        <div class="panel-header">
          <span class="panel-title">属性配置</span>
          <button class="close-btn" @click="selectedId = null">✕</button>
        </div>
        <div class="panel-content">
          <div class="property-group">
            <label class="property-label">组件名称</label>
            <input 
              type="text" 
              class="property-input"
              v-model="selectedItem.config.label"
              placeholder="输入组件名称"
            />
          </div>
          
          <div class="property-group" v-if="selectedItem.type === 'input' || selectedItem.type === 'textarea'">
            <label class="property-label">占位文本</label>
            <input 
              type="text" 
              class="property-input"
              v-model="selectedItem.config.placeholder"
              placeholder="输入占位文本"
            />
          </div>
          
          <div class="property-group" v-if="selectedItem.type === 'select'">
            <label class="property-label">选项列表</label>
            <textarea 
              class="property-textarea"
              v-model="selectOptionsText"
              placeholder="每行一个选项"
              @input="updateSelectOptions"
            ></textarea>
          </div>
          
          <div class="property-group">
            <label class="property-label">宽度</label>
            <input 
              type="number" 
              class="property-input"
              v-model="selectedItem.config.width"
              placeholder="宽度"
            />
          </div>
          
          <div class="property-group">
            <label class="property-label">是否必填</label>
            <label class="checkbox-label">
              <input 
                type="checkbox" 
                v-model="selectedItem.config.required"
              />
              <span>必填</span>
            </label>
          </div>
          
          <div class="property-group">
            <label class="property-label">显示隐藏</label>
            <label class="checkbox-label">
              <input 
                type="checkbox" 
                v-model="selectedItem.config.hidden"
              />
              <span>隐藏</span>
            </label>
          </div>
          
          <div class="property-actions">
            <button class="btn btn-danger" @click="deleteItem">删除组件</button>
          </div>
        </div>
      </aside>
    </main>
    
    <footer class="footer">
      <span>低代码平台 v1.0.0 | 基于 Vue3 + Vite 构建</span>
    </footer>
  </div>
</template>

<script setup>
import { ref, computed, markRaw } from 'vue'
import LcInput from './components/LcInput.vue'
import LcTextarea from './components/LcTextarea.vue'
import LcSelect from './components/LcSelect.vue'
import LcButton from './components/LcButton.vue'
import LcSwitch from './components/LcSwitch.vue'
import LcRadio from './components/LcRadio.vue'
import LcCheckbox from './components/LcCheckbox.vue'
import LcDatePicker from './components/LcDatePicker.vue'
import LcCard from './components/LcCard.vue'

const activeNav = ref('editor')
const previewMode = ref(false)
const canvasItems = ref([])
const selectedId = ref(null)
const dragComponent = ref(null)

const navItems = [
  { key: 'editor', label: '编辑器' },
  { key: 'preview', label: '预览' },
  { key: 'schema', label: 'JSON' }
]

const componentList = [
  { type: 'input', name: '输入框', icon: '📝' },
  { type: 'textarea', name: '文本域', icon: '📄' },
  { type: 'select', name: '下拉框', icon: '📋' },
  { type: 'button', name: '按钮', icon: '🔘' },
  { type: 'switch', name: '开关', icon: '🔌' },
  { type: 'radio', name: '单选框', icon: '⚪' },
  { type: 'checkbox', name: '多选框', icon: '☑️' },
  { type: 'datepicker', name: '日期选择', icon: '📅' },
  { type: 'card', name: '卡片', icon: '🃏' }
]

const componentMap = {
  input: markRaw(LcInput),
  textarea: markRaw(LcTextarea),
  select: markRaw(LcSelect),
  button: markRaw(LcButton),
  switch: markRaw(LcSwitch),
  radio: markRaw(LcRadio),
  checkbox: markRaw(LcCheckbox),
  datepicker: markRaw(LcDatePicker),
  card: markRaw(LcCard)
}

const selectedItem = computed(() => {
  return canvasItems.value.find(item => item.id === selectedId.value)
})

const selectOptionsText = computed({
  get: () => {
    if (selectedItem.value?.type === 'select') {
      return selectedItem.value.config.options?.join('\n') || ''
    }
    return ''
  },
  set: (val) => {
    if (selectedItem.value?.type === 'select') {
      selectedItem.value.config.options = val.split('\n').filter(v => v.trim())
    }
  }
})

const getComponent = (type) => {
  return componentMap[type] || LcInput
}

const generateId = () => {
  return `component-${Date.now()}-${Math.random().toString(36).substr(2, 9)}`
}

const handleDragStart = (event, component) => {
  dragComponent.value = component
  event.dataTransfer.effectAllowed = 'copy'
}

const handleDragEnd = () => {
  dragComponent.value = null
}

const handleDrop = (event) => {
  if (!dragComponent.value) return
  
  const defaultConfig = {
    label: dragComponent.value.name,
    placeholder: '',
    width: 300,
    required: false,
    hidden: false,
    options: ['选项1', '选项2', '选项3']
  }
  
  canvasItems.value.push({
    id: generateId(),
    type: dragComponent.value.type,
    config: { ...defaultConfig }
  })
  
  dragComponent.value = null
}

const selectItem = (index) => {
  if (!previewMode.value) {
    selectedId.value = canvasItems.value[index].id
  }
}

const handleCanvasClick = () => {
  selectedId.value = null
}

const deleteItem = () => {
  const index = canvasItems.value.findIndex(item => item.id === selectedId.value)
  if (index > -1) {
    canvasItems.value.splice(index, 1)
    selectedId.value = null
  }
}

const clearCanvas = () => {
  canvasItems.value = []
  selectedId.value = null
}

const exportSchema = () => {
  const schema = {
    version: '1.0.0',
    components: canvasItems.value
  }
  const blob = new Blob([JSON.stringify(schema, null, 2)], { type: 'application/json' })
  const url = URL.createObjectURL(blob)
  const a = document.createElement('a')
  a.href = url
  a.download = 'lowcode-schema.json'
  a.click()
  URL.revokeObjectURL(url)
}
</script>

<style scoped>
.app-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 24px;
  height: 64px;
  background: #fff;
  border-bottom: 1px solid var(--border-color);
  box-shadow: var(--shadow-sm);
}

.header-left {
  display: flex;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  gap: 8px;
}

.logo-icon {
  font-size: 24px;
}

.logo-text {
  font-size: 18px;
  font-weight: 600;
  color: var(--primary-color);
}

.header-center {
  flex: 1;
  display: flex;
  justify-content: center;
}

.nav {
  display: flex;
  gap: 24px;
}

.nav-item {
  padding: 8px 16px;
  font-size: 14px;
  color: var(--text-secondary);
  background: transparent;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
}

.nav-item:hover {
  color: var(--primary-color);
  background: rgba(64, 128, 255, 0.1);
}

.nav-item.active {
  color: var(--primary-color);
  font-weight: 500;
  background: rgba(64, 128, 255, 0.1);
}

.header-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

.btn {
  padding: 8px 16px;
  font-size: 14px;
  border-radius: 4px;
  cursor: pointer;
  transition: all 0.2s;
  border: 1px solid transparent;
}

.btn-primary {
  background: var(--primary-color);
  color: #fff;
}

.btn-primary:hover {
  background: #609fff;
}

.btn-outline {
  background: #fff;
  color: var(--text-primary);
  border-color: var(--border-color);
}

.btn-outline:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.btn-danger {
  background: var(--error-color);
  color: #fff;
}

.btn-danger:hover {
  background: #ff7875;
}

.main-content {
  display: flex;
  flex: 1;
  overflow: hidden;
}

.sidebar {
  width: 200px;
  background: #fff;
  border-right: 1px solid var(--border-color);
  display: flex;
  flex-direction: column;
}

.sidebar-header {
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
}

.sidebar-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.component-list {
  flex: 1;
  overflow-y: auto;
  padding: 8px;
}

.component-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 12px;
  margin-bottom: 4px;
  background: #f5f7fa;
  border-radius: 6px;
  cursor: grab;
  transition: all 0.2s;
}

.component-item:hover {
  background: rgba(64, 128, 255, 0.1);
  transform: translateX(4px);
}

.component-item:active {
  cursor: grabbing;
}

.component-icon {
  font-size: 18px;
}

.component-name {
  font-size: 13px;
  color: var(--text-primary);
}

.editor-area {
  flex: 1;
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.editor-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 20px;
  background: #fff;
  border-bottom: 1px solid var(--border-color);
}

.editor-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.editor-actions {
  display: flex;
  gap: 8px;
}

.canvas {
  flex: 1;
  padding: 24px;
  overflow-y: auto;
  background: var(--bg-color);
  min-height: 500px;
}

.empty-canvas {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 400px;
  border: 2px dashed var(--border-color);
  border-radius: 12px;
  background: #fff;
}

.empty-icon {
  font-size: 48px;
  margin-bottom: 16px;
}

.empty-text {
  font-size: 14px;
  color: var(--text-placeholder);
}

.canvas-item {
  padding: 16px;
  margin-bottom: 16px;
  background: #fff;
  border-radius: 8px;
  border: 2px solid transparent;
  cursor: pointer;
  transition: all 0.2s;
}

.canvas-item:hover {
  box-shadow: var(--shadow-md);
}

.canvas-item.selected {
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(64, 128, 255, 0.1);
}

.properties-panel {
  width: 320px;
  background: #fff;
  border-left: 1px solid var(--border-color);
  display: flex;
  flex-direction: column;
}

.panel-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 16px;
  border-bottom: 1px solid var(--border-color);
}

.panel-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
}

.close-btn {
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: transparent;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  color: var(--text-secondary);
  font-size: 14px;
}

.close-btn:hover {
  background: var(--bg-color);
  color: var(--text-primary);
}

.panel-content {
  flex: 1;
  padding: 16px;
  overflow-y: auto;
}

.property-group {
  margin-bottom: 20px;
}

.property-label {
  display: block;
  font-size: 13px;
  color: var(--text-secondary);
  margin-bottom: 8px;
}

.property-input {
  width: 100%;
  padding: 8px 12px;
  font-size: 14px;
  border: 1px solid var(--border-color);
  border-radius: 4px;
  outline: none;
  transition: border-color 0.2s;
}

.property-input:focus {
  border-color: var(--primary-color);
}

.property-textarea {
  width: 100%;
  padding: 8px 12px;
  font-size: 14px;
  border: 1px solid var(--border-color);
  border-radius: 4px;
  outline: none;
  transition: border-color 0.2s;
  resize: vertical;
  min-height: 80px;
}

.property-textarea:focus {
  border-color: var(--primary-color);
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
  color: var(--text-primary);
  cursor: pointer;
}

.checkbox-label input {
  width: 16px;
  height: 16px;
  cursor: pointer;
}

.property-actions {
  display: flex;
  gap: 12px;
  margin-top: 24px;
  padding-top: 16px;
  border-top: 1px solid var(--border-color);
}

.footer {
  padding: 12px 24px;
  background: #fff;
  border-top: 1px solid var(--border-color);
  text-align: center;
  font-size: 12px;
  color: var(--text-placeholder);
}
</style>