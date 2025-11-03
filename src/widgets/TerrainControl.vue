<template>
  <div class="terrain-control">
    <h4>地形设置</h4>
    
    <div class="control-group">
      <div class="control-item">
        <label class="checkbox-label">
          <input 
            type="checkbox" 
            v-model="terrainEnabled" 
            @change="handleTerrainToggle"
          >
          <span class="checkmark"></span>
          启用地形
        </label>
      </div>
    </div>
    
    <div v-if="terrainEnabled" class="terrain-options">
      <div class="option-item">
        <label class="option-label">地形类型:</label>
        <select v-model="selectedTerrainType" class="terrain-select">
          <option value="cesium-world">Cesium世界地形</option>
          <option value="arcgis">ArcGIS全球地形</option>
          <option value="local">本地地形数据</option>
        </select>
      </div>
      
      <div class="option-item">
        <label class="option-label">地形夸张:</label>
        <input 
          type="range" 
          min="1" 
          max="3" 
          step="0.1"
          v-model="terrainExaggeration" 
          class="exaggeration-slider"
        >
        <span class="exaggeration-value">{{ terrainExaggeration }}x</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { useTerrain } from '@/utils/composables/useTerrain'

// 使用地形管理
const { 
  terrainEnabled, 
  terrainType, 
  terrainExaggeration, 
  toggleTerrain, 
  setTerrainExaggeration 
} = useTerrain()

// 地形状态
const selectedTerrainType = ref(terrainType.value)

// 地形切换处理
const handleTerrainToggle = () => {
  toggleTerrain(terrainEnabled.value, selectedTerrainType.value)
}

// 监听地形类型变化
watch(selectedTerrainType, (newType) => {
  if (terrainEnabled.value) {
    toggleTerrain(true, newType)
  }
})

// 监听地形夸张系数变化
watch(terrainExaggeration, (newValue) => {
  setTerrainExaggeration(newValue)
})
</script>

<style scoped>
.terrain-control {
  margin-bottom: 24px;
}

.terrain-options {
  margin-top: 12px;
  padding: 12px;
  background: #f8f9fa;
  border-radius: 4px;
  border-left: 3px solid #52c41a;
}

.option-item {
  display: flex;
  align-items: center;
  margin-bottom: 12px;
}

.option-item:last-child {
  margin-bottom: 0;
}

.option-label {
  font-size: 13px;
  color: #666;
  min-width: 80px;
  margin-right: 12px;
}

.terrain-select {
  flex: 1;
  padding: 6px 8px;
  border: 1px solid #d9d9d9;
  border-radius: 4px;
  font-size: 13px;
}

.exaggeration-slider {
  flex: 1;
  margin-right: 12px;
}

.exaggeration-value {
  font-size: 12px;
  color: #999;
  min-width: 30px;
  text-align: center;
}
</style>