<template>
  <div v-if="show" class="notification" :class="{ 'error': isError }">
    <div class="notification-content">
      <span class="message">{{ message }}</span>
      <button class="close-btn" @click="$emit('close')">×</button>
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  show: boolean
  message: string
  isError?: boolean
}>()

defineEmits<{
  "close": []
}>()
</script>

<style scoped lang="less">
.notification {
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(76, 175, 80, 0.95);
  color: white;
  padding: 15px 20px;
  border-radius: 6px;
  min-width: 300px;
  max-width: 400px;
  z-index: 2000;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  animation: slideIn 0.3s ease-out;
}

.notification.error {
  background: rgba(244, 67, 54, 0.95);
}

.notification-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.message {
  font-size: 14px;
  line-height: 1.5;
  flex: 1;
}

.close-btn {
  background: none;
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  padding: 0;
  margin-left: 15px;
  line-height: 1;
  opacity: 0.8;
  transition: opacity 0.3s;
}

.close-btn:hover {
  opacity: 1;
}

@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@media (max-width: 768px) {
  .notification {
    left: 20px;
    right: 20px;
    max-width: none;
  }
}
</style>
