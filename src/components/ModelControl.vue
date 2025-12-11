<template>
  <div class="model-control">
    <h4>模型控制</h4>
    <div class="control-item">
      <label class="switch">
        <input 
          type="checkbox" 
          :checked="modelsVisible"
          @change="$emit('toggle-models')"
        >
        <span class="slider"></span>
      </label>
      <span class="control-label">显示三维模型</span>
    </div>
    <div class="control-buttons">
      <button class="reset-btn" @click="$emit('reset-view')">
        重置视图
      </button>
    </div>
    <p class="control-description">控制三维考古模型的显示与隐藏</p>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  modelsVisible: boolean
}>()

defineEmits<{
  "toggle-models": []
  "reset-view": []
}>()
</script>

<style scoped lang="less">
.model-control {
  padding: 15px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

h4 {
  margin: 0 0 15px 0;
  font-size: 14px;
  font-weight: normal;
  color: #ccc;
}

.control-item {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.control-label {
  margin-left: 10px;
  font-size: 14px;
  color: #fff;
}

.control-buttons {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

.reset-btn {
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: white;
  padding: 8px 15px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.reset-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}

.control-description {
  margin: 8px 0 0 0;
  font-size: 12px;
  color: #999;
  line-height: 1.4;
}

/* 开关样式 */
.switch {
  position: relative;
  display: inline-block;
  width: 50px;
  height: 24px;
}

.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #666;
  transition: .3s;
  border-radius: 24px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 18px;
  width: 18px;
  left: 3px;
  bottom: 3px;
  background-color: white;
  transition: .3s;
  border-radius: 50%;
}

input:checked + .slider {
  background-color: #4CAF50;
}

input:checked + .slider:before {
  transform: translateX(26px);
}
</style>
