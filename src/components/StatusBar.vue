<template>
  <div class="status-bar">
    <div class="status-item">
      <span class="label">位置：</span>
      <span class="value">{{ mousePosition.lon }}, {{ mousePosition.lat }}</span>
    </div>
    <div class="status-item">
      <span class="label">高程：</span>
      <span class="value">{{ terrainElevation }}</span>
    </div>
    <div class="status-item">
      <span class="label">高度：</span>
      <span class="value">{{ viewHeight }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
interface MousePosition {
  lon: string
  lat: string
}

defineProps<{
  mousePosition: MousePosition
  terrainElevation: string
  viewHeight: string
}>()
</script>

<style scoped lang="less">
.status-bar {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(40, 40, 40, 0.9);
  color: white;
  display: flex;
  padding: 8px 15px;
  font-size: 12px;
  z-index: 1000;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.status-item {
  margin-right: 30px;
  display: flex;
  align-items: center;
}

.status-item:last-child {
  margin-right: 0;
}

.label {
  color: #aaa;
  margin-right: 5px;
}

.value {
  color: #fff;
  font-weight: 500;
  min-width: 100px;
}

@media (max-width: 768px) {
  .status-bar {
    flex-direction: column;
    gap: 5px;
  }
  
  .status-item {
    margin-right: 0;
  }
}
</style>
