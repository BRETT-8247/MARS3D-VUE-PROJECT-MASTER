<template>
  <div class="archaeology-main-view">
    <!-- 地图容器 -->
    <div id="cesiumContainer" class="cesium-container"></div>
    
    <!-- 控制面板 -->
    <div class="control-panel" :style="{ display: panelVisible ? 'block' : 'none' }">
      <div class="panel-header">
        <h3>展示系统</h3>
        <button class="toggle-btn" @click="togglePanel">隐藏</button>
      </div>
      
      <!-- 面板内容区域（添加滚动条） -->
      <div class="panel-content">
        <!-- 地区选择器 -->
        <RegionSelector
          :regions="regions"
          :current-region="currentRegion"
          @region-change="switchRegion"
        />
        
        <!-- 图层控制 -->
        <LayerControl 
          :static-layer-visible="staticLayerVisible"
          @toggle-static-layer="toggleStaticLayer"
        />
        
        <!-- 地形控制 -->
        <TerrainControl
          :terrain-enabled="terrainEnabled"
          @toggle-terrain="toggleTerrain"
        />
        
        <!-- 模型控制 -->
        <ModelControl
          :models-visible="modelsVisible"
          @toggle-models="toggleModels"
          @reset-view="resetView"
        />
      </div>
    </div>

    <!-- 显示面板按钮 -->
    <button 
      v-show="!panelVisible"
      class="show-panel-btn"
      @click="togglePanel"
    >
      显示面板
    </button>
    
    <!-- 状态栏 -->
    <StatusBar
      :mouse-position="mousePosition"
      :terrain-elevation="terrainElevation"
      :view-height="viewHeight"
    />
    
    <!-- 加载指示器 -->
    <LoadingIndicator :loading="loading" />
    
    <!-- 通知 -->
    <Notification
      :show="notification.show"
      :message="notification.message"
      :is-error="notification.isError"
      @close="notification.show = false"
    />
    
    <!-- 自定义信息框 -->
    <CustomInfoBox
      :show="infoBox.show"
      :content="infoBox.content"
      @close="closeInfoBox"
    />
  </div>
</template>

<script setup lang="ts">
import { onMounted, onUnmounted, reactive, ref, computed } from "vue"
import LayerControl from "./LayerControl.vue"
import TerrainControl from "./TerrainControl.vue"
import ModelControl from "./ModelControl.vue"
import StatusBar from "./StatusBar.vue"
import LoadingIndicator from "./LoadingIndicator.vue"
import Notification from "./Notification.vue"
import CustomInfoBox from "./CustomInfoBox.vue"
import RegionSelector from "./RegionSelector.vue"

// 定义地区类型
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

// 响应式数据
const panelVisible = ref(true)
const staticLayerVisible = ref(true)
const terrainEnabled = ref(false)
const modelsVisible = ref(true)
const loading = ref(true)
const currentRegion = ref("current")

const mousePosition = reactive({
  lon: "--",
  lat: "--"
})

const terrainElevation = ref("--")
const viewHeight = ref("--")

const notification = reactive({
  show: false,
  message: "",
  isError: false
})

const infoBox = reactive({
  show: false,
  content: ""
})

// 地区配置
const regions = ref<Region[]>([
  {
    id: "current",
    name: "当前考古遗址",
    center: [106.9963259, 33.43743713],
    height: 1200,
    models: ["http://10.101.242.103:8081/3dtiles/model1/tileset.json"],
    terrain: {
      enabled: false,
      url: "https://elevation3d.arcgis.com/arcgis/rest/services/WorldElevation3D/Terrain3D/ImageServer"
    },
    imagery: {
      url: "http://10.101.242.103:8081/Greatwall/长城神木/{z}/{x}/{y}.png"
    },
    sites: [
      {
        id: "site1",
        name: "考古遗址",
        position: [106.9963259, 33.43743713, 671.8369552],
        pointColor: "#FFD700",
        labelText: "📍 考古遗址点1",
        description: `
          <div class="site-info-content">
            <div class="site-header">
              <h3>🏺 考古遗址点1</h3>
              <div class="site-badge">重要遗址</div>
            </div>
            <div class="info-grid">
              <div class="info-item">
                <span class="info-label">📍 地理位置：</span>
                <span class="info-value">北纬 33.43743713°，东经 106.9963259°</span>
              </div>
              <div class="info-item">
                <span class="info-label">⛰️ 海拔高度：</span>
                <span class="info-value">671.84 米</span>
              </div>
              <div class="info-item">
                <span class="info-label">🔍 遗址类型：</span>
                <span class="info-value">古代文明遗址</span>
              </div>
              <div class="info-item">
                <span class="info-label">📅 发现时间：</span>
                <span class="info-value">2020年</span>
              </div>
              <div class="info-item">
                <span class="info-label">⏳ 文化时期：</span>
                <span class="info-value">青铜时代晚期</span>
              </div>
            </div>
            <div class="site-features">
              <h4>🏛️ 主要发现</h4>
              <ul>
                <li>青铜器文物</li>
                <li>陶器碎片</li>
                <li>建筑遗址</li>
              </ul>
            </div>
            <div class="site-tip">
              <p>💡 双击此标记可聚焦查看遗址区域</p>
            </div>
          </div>
        `
      }
    ]
  },
  {
    id: "p2",
    name: "p2考古遗址",
    center: [108.9963259, 36.43743713],
    height: 1200,
    models: [],
    terrain: {
      enabled: false,
      url: "https://elevation3d.arcgis.com/arcgis/rest/services/WorldElevation3D/Terrain3D/ImageServer"
    },
    imagery: {
      url: "http://10.101.242.103:8081/Greatwall/tiles/{z}/{x}/{y}.png"
    },
    sites: [
      {
        id: "site1",
        name: "考古遗址",
        position: [108.9963259, 36.43743713, 671.8369552],
        pointColor: "#FFD700",
        labelText: "📍 考古遗址点2",
        description: `
          <div class="site-info-content">
            <div class="site-header">
              <h3>🏺 考古遗址点2</h3>
              <div class="site-badge">重要遗址</div>
            </div>
            <div class="info-grid">
              <div class="info-item">
                <span class="info-label">📍 地理位置：</span>
                <span class="info-value">北纬 33.43743713°，东经 106.9963259°</span>
              </div>
              <div class="info-item">
                <span class="info-label">⛰️ 海拔高度：</span>
                <span class="info-value">671.84 米</span>
              </div>
              <div class="info-item">
                <span class="info-label">🔍 遗址类型：</span>
                <span class="info-value">古代文明遗址</span>
              </div>
              <div class="info-item">
                <span class="info-label">📅 发现时间：</span>
                <span class="info-value">2020年</span>
              </div>
              <div class="info-item">
                <span class="info-label">⏳ 文化时期：</span>
                <span class="info-value">青铜时代晚期</span>
              </div>
            </div>
            <div class="site-features">
              <h4>🏛️ 主要发现</h4>
              <ul>
                <li>青铜器文物</li>
                <li>陶器碎片</li>
                <li>建筑遗址</li>
              </ul>
            </div>
            <div class="site-tip">
              <p>💡 双击此标记可聚焦查看遗址区域</p>
            </div>
          </div>
        `
      }
    ]
  }
])

// 计算当前地区数据
const currentRegionData = computed(() => {
  return regions.value.find(region => region.id === currentRegion.value)
})

// Cesium相关变量
let viewer: any = null
const tilesets: any[] = []
let siteEntities: any[] = []
let staticImageryLayer: any = null

// 工具函数
const showNotification = (message: string, isError = false) => {
  notification.message = message
  notification.isError = isError
  notification.show = true
}

const getPreciseTerrainHeight = async (longitude: number, latitude: number) => {
  try {
    const position = (window as any).Cesium.Cartographic.fromRadians(longitude, latitude)
    const updatedPositions = await (window as any).Cesium.sampleTerrainMostDetailed(viewer.terrainProvider, [position])
    
    if (updatedPositions && updatedPositions.length > 0) {
      return updatedPositions[0].height
    }
  } catch (error) {
    console.warn("地形采样失败:", error)
  }
  return null
}

// 初始化Cesium
async function initCesium() {
  // 设置Cesium Ion Token
  (window as any).Cesium.Ion.defaultAccessToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI1MDc4OTVlMC04ODRhLTQxMDMtODJkZS1mNTdjMzFhZTkwYTIiLCJpZCI6MzQ0ODA3LCJpYXQiOjE3NjUyODA5Nzl9.63R_RluVKzbspagos17ZIgfox2FYJBTdWJK7Re99DlQ"
  
  // 创建Cesium Viewer
  viewer = new (window as any).Cesium.Viewer("cesiumContainer", {
    animation: false,
    vrButton: false,
    timeline: false,
    sceneModePicker: false,
    scene3DOnly: true,
    infoBox: false,
    navigationHelpButton: false,
    baseLayerPicker: false,
    terrainProvider: new (window as any).Cesium.EllipsoidTerrainProvider()
  })
  
  // 设置亮度效果
  const stages = viewer.scene.postProcessStages
  viewer.scene.brightness = viewer.scene.brightness || stages.add((window as any).Cesium.PostProcessStageLibrary.createBrightnessStage())
  viewer.scene.brightness.enabled = true
  viewer.scene.brightness.uniforms.brightness = Number(1.2)
  
  // 设置主页按钮行为
  viewer.homeButton.viewModel.command.beforeExecute.addEventListener((cmd: any) => {
    const region = currentRegionData.value
    if (region) {
      flyToRegion(region)
    }
    cmd.cancel = true
  })
  
  // 设置事件处理器
  setupEventHandlers()
  
  // 始终加载天地图底图
  loadTianDiTuImagery()
  
  // 加载当前地区数据
  await loadCurrentRegionData()
  
  // 隐藏加载指示器
  loading.value = false
}

// 设置事件处理器
function setupEventHandlers() {
  // 双击聚焦
  viewer.cesiumWidget.screenSpaceEventHandler.setInputAction((event: any) => {
    const pickedObject = viewer.scene.pick(event.position)
    if ((window as any).Cesium.defined(pickedObject) && pickedObject.id) {
      flyToSite(pickedObject.id)
    }
  }, (window as any).Cesium.ScreenSpaceEventType.LEFT_DOUBLE_CLICK)
  
  // 鼠标移动显示信息
  viewer.cesiumWidget.screenSpaceEventHandler.setInputAction((event: any) => {
    const pickedObject = viewer.scene.pick(event.endPosition)
    
    if ((window as any).Cesium.defined(pickedObject) && pickedObject.id) {
      if (siteEntities.includes(pickedObject.id)) {
        showInfoBox(pickedObject.id)
      } else {
        closeInfoBox()
      }
    } else {
      closeInfoBox()
    }
  }, (window as any).Cesium.ScreenSpaceEventType.MOUSE_MOVE)
  
  // 鼠标位置跟踪
  const handler = new (window as any).Cesium.ScreenSpaceEventHandler(viewer.scene.canvas)
  handler.setInputAction((movement: any) => {
    updateMousePosition(movement.endPosition)
  }, (window as any).Cesium.ScreenSpaceEventType.MOUSE_MOVE)
}

// 更新鼠标位置信息
function updateMousePosition(screenPosition: any) {
  const cartesian = viewer.camera.pickEllipsoid(
    screenPosition, 
    viewer.scene.globe.ellipsoid
  )
  
  if (cartesian) {
    const cartographic = (window as any).Cesium.Cartographic.fromCartesian(cartesian)
    const longitude = (window as any).Cesium.Math.toDegrees(cartographic.longitude).toFixed(6)
    const latitude = (window as any).Cesium.Math.toDegrees(cartographic.latitude).toFixed(6)
    
    mousePosition.lon = longitude
    mousePosition.lat = latitude
    
    if (terrainEnabled.value) {
      const terrainPosition = (window as any).Cesium.Cartographic.fromCartesian(cartesian)
      const terrainSample = (window as any).Cesium.sampleTerrainMostDetailed(viewer.terrainProvider, [terrainPosition])
      terrainSample.then((updatedPositions: any) => {
        if (updatedPositions && updatedPositions.length > 0) {
          const terrainHeight = updatedPositions[0].height.toFixed(2)
          terrainElevation.value = `${terrainHeight} 米`
        }
      }).catch(() => {
        terrainElevation.value = "-"
      })
    } else {
      terrainElevation.value = "-"
    }
  }
  
  const height = Math.round(viewer.camera.positionCartographic.height)
  viewHeight.value = `${height} 米`
}

// 加载天地图影像（始终加载）
function loadTianDiTuImagery() {
  try {
    viewer.imageryLayers.removeAll()
    
    const tdtLayer = viewer.imageryLayers.addImageryProvider(new (window as any).Cesium.WebMapTileServiceImageryProvider({
      url: "http://t0.tianditu.gov.cn/img_w/wmts?tk=59d6fbb8bb0196c2e559d58a41903208",
      layer: "img",
      style: "default",
      format: "tiles",
      tileMatrixSetID: "w",
      maximumLevel: 18
    }))
    
    console.log("天地图底图加载成功")
    return tdtLayer
  } catch (error) {
    console.error("天地图影像加载失败:", error)
    return null
  }
}

// 加载静态影像
function loadStaticImagery(url: string) {
  try {
    // 移除之前的静态影像层
    if (staticImageryLayer) {
      viewer.imageryLayers.remove(staticImageryLayer)
      staticImageryLayer = null
    }
    
    staticImageryLayer = viewer.imageryLayers.addImageryProvider(new (window as any).Cesium.UrlTemplateImageryProvider({
      url,
      tilingScheme: new (window as any).Cesium.GeographicTilingScheme(),
      minimumLevel: 0,
      maximumLevel: 18
    }))
    
    // 设置黑色背景为透明
    staticImageryLayer.colorToAlpha = (window as any).Cesium.Color.BLACK
    staticImageryLayer.colorToAlphaThreshold = 0.02
    
    // 设置初始可见性
    staticImageryLayer.show = staticLayerVisible.value
    
    showNotification("静态影像加载成功")
    return staticImageryLayer
  } catch (error: any) {
    console.error("静态影像加载失败:", error)
    showNotification("静态影像加载失败: " + error.message, true)
    return null
  }
}

// 切换静态影像图层的显示状态
function toggleStaticLayer() {
  staticLayerVisible.value = !staticLayerVisible.value
  
  if (staticImageryLayer) {
    staticImageryLayer.show = staticLayerVisible.value
    showNotification(staticLayerVisible.value ? "静态影像已显示" : "静态影像已隐藏")
  } else {
    showNotification("没有可显示的静态影像", true)
  }
}

// 加载地形
async function loadTerrain(url: string) {
  try {
    const terrainProvider = await (window as any).Cesium.ArcGISTiledElevationTerrainProvider.fromUrl(url)
    viewer.terrainProvider = terrainProvider
    viewer.scene.globe.depthTestAgainstTerrain = true
    terrainEnabled.value = true
    showNotification("地形数据加载成功")
    return terrainProvider
  } catch (error) {
    console.error("地形数据加载失败:", error)
    showNotification("地形数据加载失败", true)
    viewer.terrainProvider = new (window as any).Cesium.EllipsoidTerrainProvider()
    terrainEnabled.value = false
    return null
  }
}

// 禁用地形
function disableTerrain() {
  viewer.terrainProvider = new (window as any).Cesium.EllipsoidTerrainProvider()
  viewer.scene.globe.depthTestAgainstTerrain = false
  terrainEnabled.value = false
  showNotification("地形已禁用")
}

// 加载3D模型
async function load3DTiles(url: string) {
  try {
    const tileset = await (window as any).Cesium.Cesium3DTileset.fromUrl(url, {
      maximumScreenSpaceError: 16,
      maximumNumberOfLoadedTiles: 1024
    })
    
    viewer.scene.primitives.add(tileset)
    
    if (terrainEnabled.value) {
      await adjustTilesetToTerrain(tileset)
    }
    
    tilesets.push(tileset)
    showNotification("三维模型加载成功")
    return tileset
  } catch (error) {
    console.error("三维模型加载失败:", error)
    showNotification("三维模型加载失败", true)
    throw error
  }
}

// 调整模型高度贴合地形
async function adjustTilesetToTerrain(tileset: any) {
  try {
    await tileset.readyPromise
    const boundingSphere = tileset.boundingSphere
    const cartographic = (window as any).Cesium.Cartographic.fromCartesian(boundingSphere.center)
    
    const terrainHeight = await getPreciseTerrainHeight(cartographic.longitude, cartographic.latitude)
    
    if (terrainHeight !== null) {
      const modelBottomHeight = cartographic.height - boundingSphere.radius
      const heightOffset = terrainHeight - modelBottomHeight
      
      const surfacePosition = (window as any).Cesium.Cartesian3.fromRadians(cartographic.longitude, cartographic.latitude, 0)
      const offsetPosition = (window as any).Cesium.Cartesian3.fromRadians(cartographic.longitude, cartographic.latitude, heightOffset)
      const translation = (window as any).Cesium.Cartesian3.subtract(offsetPosition, surfacePosition, new (window as any).Cesium.Cartesian3())
      
      tileset.modelMatrix = (window as any).Cesium.Matrix4.fromTranslation(translation)
      return true
    }
  } catch (error) {
    console.warn("无法调整模型高度:", error)
  }
  return false
}

// 创建遗址点实体
function createSiteEntity(site: Site) {
  const entity = viewer.entities.add({
    name: site.name,
    position: (window as any).Cesium.Cartesian3.fromDegrees(...site.position),
    point: {
      pixelSize: 24,
      color: (window as any).Cesium.Color.fromCssColorString(site.pointColor),
      outlineColor: (window as any).Cesium.Color.fromCssColorString("#8B4513"),
      outlineWidth: 3,
      heightReference: terrainEnabled.value 
        ? (window as any).Cesium.HeightReference.CLAMP_TO_GROUND 
        : (window as any).Cesium.HeightReference.NONE,
      disableDepthTestDistance: Number.POSITIVE_INFINITY,
      scaleByDistance: new (window as any).Cesium.NearFarScalar(1500, 1.5, 8000, 0.5)
    },
    label: {
      text: site.labelText,
      font: "bold 16px 'Microsoft YaHei', 'PingFang SC', 'Helvetica Neue', Arial, sans-serif",
      fillColor: (window as any).Cesium.Color.fromCssColorString("#FFFFFF"),
      backgroundColor: (window as any).Cesium.Color.fromCssColorString("rgba(139, 69, 19, 0.85)"),
      backgroundPadding: new (window as any).Cesium.Cartesian2(12, 8),
      pixelOffset: new (window as any).Cesium.Cartesian2(0, -50),
      showBackground: true,
      heightReference: terrainEnabled.value 
        ? (window as any).Cesium.HeightReference.CLAMP_TO_GROUND 
        : (window as any).Cesium.HeightReference.NONE,
      disableDepthTestDistance: Number.POSITIVE_INFINITY,
      scaleByDistance: new (window as any).Cesium.NearFarScalar(1500, 1.2, 8000, 0.6),
      style: (window as any).Cesium.LabelStyle.FILL_AND_OUTLINE,
      outlineWidth: 2,
      outlineColor: (window as any).Cesium.Color.fromCssColorString("#000000")
    },
    description: site.description
  })
  
  siteEntities.push(entity)
  return entity
}

// 飞行到地区
function flyToRegion(region: Region) {
  viewer.camera.flyTo({
    destination: (window as any).Cesium.Cartesian3.fromDegrees(
      region.center[0],
      region.center[1], 
      region.height
    ),
    orientation: {
      heading: (window as any).Cesium.Math.toRadians(0),
      pitch: (window as any).Cesium.Math.toRadians(-45),
      roll: 0
    },
    duration: 2
  })
}

// 飞行到遗址点
function flyToSite(entity: any) {
  if (!entity) { 
    return
  }
  
  const position = entity.position.getValue((window as any).Cesium.JulianDate.now())
  
  viewer.camera.flyTo({
    destination: (window as any).Cesium.Cartesian3.fromDegrees(
      (window as any).Cesium.Math.toDegrees((window as any).Cesium.Cartographic.fromCartesian(position).longitude),
      (window as any).Cesium.Math.toDegrees((window as any).Cesium.Cartographic.fromCartesian(position).latitude),
      1200
    ),
    orientation: {
      heading: (window as any).Cesium.Math.toRadians(0),
      pitch: (window as any).Cesium.Math.toRadians(-45),
      roll: 0
    },
    duration: 2,
    complete: () => {
      showNotification("已聚焦到该区域")
    }
  })
}

// 显示信息框
function showInfoBox(entity: any) {
  if (!entity || !entity.description) {
    return
  }
  infoBox.content = entity.description
  infoBox.show = true
}

// 关闭信息框
function closeInfoBox() {
  infoBox.show = false
}

// 切换地区
async function switchRegion(regionId: string) {
  currentRegion.value = regionId
  const region = regions.value.find(r => r.id === regionId)
  
  if (!region) {
    return
  }
  // 飞行到新地区
  flyToRegion(region)
  
  // 重新加载该地区的数据
  await loadRegionData(region)
  
  showNotification(`已切换到${region.name}`)
}

// 加载地区数据
async function loadRegionData(region: Region) {
  loading.value = true
  
  try {
    // 清除现有模型
    tilesets.forEach(tileset => {
      viewer.scene.primitives.remove(tileset)
    })
    tilesets.length = 0
    
    // 清除现有实体
    siteEntities.forEach(entity => {
      viewer.entities.remove(entity)
    })
    siteEntities = []
    
    // 加载地形数据
    if (region.terrain.enabled) {
      await loadTerrain(region.terrain.url)
    } else {
      disableTerrain()
    }
    
    // 加载静态影像
    if (region.imagery && region.imagery.url) {
      loadStaticImagery(region.imagery.url)
    } else {
      // 如果地区没有配置静态影像，清除现有的静态影像图层
      if (staticImageryLayer) {
        viewer.imageryLayers.remove(staticImageryLayer)
        staticImageryLayer = null
      }
    }
    
    // 加载模型
    if (region.models && region.models.length > 0) {
      for (const url of region.models) {
        try {
          await load3DTiles(url)
        } catch (error) {
          console.error(`模型加载失败: ${url}`, error)
          showNotification(`模型加载失败: ${url}`, true)
        }
      }
    }
    
    // 创建遗址点
    if (region.sites && region.sites.length > 0) {
      region.sites.forEach(site => {
        createSiteEntity(site)
      })
    }
    
    modelsVisible.value = true
  } finally {
    loading.value = false
  }
}

// 加载当前地区数据
async function loadCurrentRegionData() {
  const region = currentRegionData.value
  if (region) {
    await loadRegionData(region)
  }
}

// UI控制方法
const togglePanel = () => {
  panelVisible.value = !panelVisible.value
}

const toggleTerrain = async (enabled: boolean) => {
  const region = currentRegionData.value
  
  if (enabled && region && region.terrain) {
    await loadTerrain(region.terrain.url)
  } else {
    disableTerrain()
  }
}

const toggleModels = () => {
  if (tilesets.length > 0) {
    modelsVisible.value = !modelsVisible.value
    tilesets.forEach(tileset => {
      tileset.show = modelsVisible.value
    })
    showNotification(modelsVisible.value ? "所有模型已显示" : "所有模型已隐藏")
  } else {
    showNotification("没有加载的模型", true)
  }
}

const resetView = () => {
  const region = currentRegionData.value
  if (region) {
    flyToRegion(region)
    showNotification("视图已重置")
  }
}

// 组件生命周期
onMounted(() => {
  initCesium()
})

onUnmounted(() => {
  if (viewer) {
    viewer.destroy()
    viewer = null
  }
})
</script>

<style scoped lang="less">
.archaeology-main-view {
  width: 100%;
  height: 100%;
  position: relative;
}

.cesium-container {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
}

.control-panel {
  position: absolute;
  top: 10px;
  left: 10px;
  width: 320px;
  max-height: 80vh; /* 设置最大高度为视口的80% */
  background: rgba(40, 40, 40, 0.9);
  border-radius: 8px;
  color: white;
  z-index: 1000;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.5);
  transition: all 0.3s ease;
  display: flex;
  flex-direction: column;
  overflow: hidden; /* 隐藏超出部分 */
}

.panel-header {
  padding: 12px 15px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-shrink: 0; /* 防止header被压缩 */
}

.panel-header h3 {
  margin: 0;
  font-size: 16px;
  font-weight: normal;
}

.toggle-btn {
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
}

.toggle-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}

/* 面板内容区域 - 添加滚动条 */
.panel-content {
  flex: 1;
  overflow-y: auto; /* 垂直滚动条 */
  overflow-x: hidden; /* 隐藏水平滚动条 */
  max-height: calc(80vh - 50px); /* 减去header高度 */
  
  /* 自定义滚动条样式 */
  &::-webkit-scrollbar {
    width: 6px;
  }
  
  &::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.05);
    border-radius: 3px;
  }
  
  &::-webkit-scrollbar-thumb {
    background: rgba(255, 255, 255, 0.2);
    border-radius: 3px;
    
    &:hover {
      background: rgba(255, 255, 255, 0.3);
    }
  }
}

.show-panel-btn {
  position: absolute;
  top: 10px;
  left: 10px;
  background: rgba(40, 40, 40, 0.9);
  border: 1px solid rgba(255, 255, 255, 0.3);
  color: white;
  padding: 8px 12px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 12px;
  z-index: 1000;
}

.show-panel-btn:hover {
  background: rgba(40, 40, 40, 1);
}

/* 移动设备适配 */
@media (max-width: 768px) {
  .control-panel {
    width: 90%;
    left: 5%;
    max-height: 85vh; /* 在移动设备上使用更大比例 */
  }
  
  .panel-content {
    max-height: calc(85vh - 50px);
  }
  
  .show-panel-btn {
    left: 5%;
  }
}
</style>
