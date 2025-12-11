<template>
  <div v-if="loading" class="loading-indicator">
    <div class="spinner"></div>
    <span class="loading-text">加载中...</span>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  loading: boolean
}>()
</script>

<style scoped lang="less">
.loading-indicator {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: rgba(40, 40, 40, 0.9);
  color: white;
  padding: 20px 30px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  gap: 15px;
  z-index: 2000;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
}

.spinner {
  width: 30px;
  height: 30px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-top-color: #4CAF50;
  border-radius: 50%;
  animation: spin 1s linear infinite;
}

.loading-text {
  font-size: 16px;
  font-weight: 500;
}

@keyframes spin {
  to {
    transform: rotate(360deg);
  }
}
</style>

