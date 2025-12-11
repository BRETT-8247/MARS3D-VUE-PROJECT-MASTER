<template>
  <div v-if="show" class="custom-info-box">
    <div class="info-box-header">
      <h3>考古遗址信息</h3>
      <button class="close-btn" @click="$emit('close')">×</button>
    </div>
    <div class="info-box-content" v-html="content"></div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  show: boolean
  content: string
}>()

defineEmits<{
  "close": []
}>()
</script>

<style scoped lang="less">
.custom-info-box {
  position: absolute;
  top: 60px;
  right: 20px;
  width: 350px;
  background: rgba(40, 40, 40, 0.95);
  color: white;
  border-radius: 8px;
  z-index: 1000;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
  overflow: hidden;
}

.info-box-header {
  padding: 15px;
  background: rgba(139, 69, 19, 0.9);
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
}

.info-box-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: normal;
}

.close-btn {
  background: none;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  padding: 0;
  line-height: 1;
  opacity: 0.8;
  transition: opacity 0.3s;
}

.close-btn:hover {
  opacity: 1;
}

.info-box-content {
  padding: 15px;
  max-height: 400px;
  overflow-y: auto;
}

/* 信息框内部内容的样式 */
.info-box-content :deep(.site-info-content) {
  font-size: 14px;
  line-height: 1.6;
}

.info-box-content :deep(.site-header) {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.info-box-content :deep(.site-header h3) {
  margin: 0;
  font-size: 18px;
  color: #FFD700;
}

.info-box-content :deep(.site-badge) {
  background: #4CAF50;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
}

.info-box-content :deep(.info-grid) {
  display: grid;
  gap: 8px;
  margin-bottom: 15px;
}

.info-box-content :deep(.info-item) {
  display: flex;
}

.info-box-content :deep(.info-label) {
  color: #aaa;
  min-width: 100px;
}

.info-box-content :deep(.info-value) {
  color: #fff;
  flex: 1;
}

.info-box-content :deep(.site-features h4) {
  margin: 0 0 10px 0;
  font-size: 16px;
  color: #FFD700;
}

.info-box-content :deep(.site-features ul) {
  margin: 0 0 15px 0;
  padding-left: 20px;
}

.info-box-content :deep(.site-features li) {
  margin-bottom: 5px;
}

.info-box-content :deep(.site-tip) {
  padding: 10px;
  background: rgba(255, 215, 0, 0.1);
  border-left: 3px solid #FFD700;
  border-radius: 4px;
  font-size: 13px;
  color: #ddd;
}

@media (max-width: 768px) {
  .custom-info-box {
    left: 20px;
    right: 20px;
    width: auto;
  }
}
</style>
