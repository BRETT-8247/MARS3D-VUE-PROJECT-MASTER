<template>
  <div class="region-selector">
    <h4>地区选择</h4>
    <div class="region-list">
      <div 
        v-for="region in regions" 
        :key="region.id"
        class="region-item"
        :class="{ 'active': region.id === currentRegion }"
        @click="$emit('region-change', region.id)"
      >
        <div class="region-info">
          <span class="region-name">{{ region.name }}</span>
          <span class="region-coords">
            {{ region.center[0].toFixed(6) }}, {{ region.center[1].toFixed(6) }}
          </span>
        </div>
        <div class="region-stats">
          <span class="stat">模型: {{ region.models.length }}</span>
          <span class="stat">遗址点: {{ region.sites.length }}</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
interface Site {
  id: string
  name: string
  position: [number, number, number]
  pointColor: string
  labelText: string
  description: string
}

interface Region {
  id: string
  name: string
  center: [number, number]
  height: number
  models: string[]
  terrain: {
    enabled: boolean
    url: string
  }
  imagery: {
    url: string
  }
  sites: Site[]
}

defineProps<{
  regions: Region[]
  currentRegion: string
}>()

defineEmits<{
  "region-change": [regionId: string]
}>()
</script>

<style scoped lang="less">
.region-selector {
  padding: 15px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

h4 {
  margin: 0 0 15px 0;
  font-size: 14px;
  font-weight: normal;
  color: #ccc;
}

.region-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.region-item {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 6px;
  padding: 12px;
  cursor: pointer;
  transition: all 0.3s;
}

.region-item:hover {
  background: rgba(255, 255, 255, 0.1);
  border-color: rgba(255, 255, 255, 0.2);
}

.region-item.active {
  background: rgba(139, 69, 19, 0.3);
  border-color: #8B4513;
}

.region-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.region-name {
  font-size: 14px;
  font-weight: 500;
  color: #fff;
}

.region-coords {
  font-size: 11px;
  color: #aaa;
  background: rgba(0, 0, 0, 0.3);
  padding: 2px 6px;
  border-radius: 3px;
}

.region-stats {
  display: flex;
  gap: 15px;
}

.stat {
  font-size: 12px;
  color: #aaa;
}

@media (max-width: 768px) {
  .region-info {
    flex-direction: column;
    align-items: flex-start;
    gap: 5px;
  }
  
  .region-stats {
    flex-wrap: wrap;
    gap: 10px;
  }
}
</style>
