<template>
  <div class="CesiumOutbox">
    <div ref="cesiumContainer" :style="{ height, width }"></div>
  </div>
</template>

<script>
import 'cesium/Build/Cesium/Widgets/widgets.css'
import { Viewer } from 'cesium'

export default {
  name: 'CesiumMap',
  props: {
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '100%'
    }
  },
  data() {
    return {
      viewer: null
    }
  },
  mounted() {
    this.initCesium()
  },
  beforeDestroy() {
    if (this.viewer) {
      this.viewer.destroy()
      this.viewer = null
    }
  },
  methods: {
    initCesium() {
      let el = this.$refs.cesiumContainer
      this.viewer = new Viewer(el, {
        selectionIndicator: false,
        infoBox: false,
        contextOptions: {
          // 硬件反走样，默认值为 1
          msaaLevel: 8,
          requestWebgl2: true
        },
        animation: false,
        timeline: false, // 底部时间线
        fullscreenButton: false, // 全屏
        vrButton: false, // VR
        sceneModePicker: false, // 选择视角的模式（球体、平铺、斜视平铺）
        baseLayerPicker: false, // 图层选择器（地形影像服务）
        navigationHelpButton: false, // 导航帮助(手势，鼠标)
        geocoder: false, // 位置查找工具
        homeButton: false // 视角返回初始位置
      })
      // 去除logo
      this.viewer.cesiumWidget.creditContainer.style.display = "none";

    }
  }
}
</script>
<style lang="scss" scoped>
.CesiumOutbox {
  width: 100%;
  height: 100%;
}
</style>

