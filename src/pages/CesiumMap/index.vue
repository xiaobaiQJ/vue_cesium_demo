<template>
  <div class="CesiumOutbox">
    <div ref="cesiumContainer" :style="{ height: height, width: width }"></div>
  </div>
</template>

<script>
import {
  Viewer,
  GeoJsonDataSource,
  Color,
  CustomDataSource,
  HeadingPitchRange,
  Math as CesiumMath
} from 'cesium'
import 'cesium/Build/Cesium/Widgets/widgets.css'
import ChinaJsonData from './data/100000_full.json'
import BeiJingJsonData from './data/110000_full.json'

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

      this.viewer.scene.globe.baseColor = Color.BLACK // 设置地球颜色
      this.viewer.cesiumWidget.creditContainer.style.display = "none";// 去除logo
      this.loadGeoJsonDataSource(ChinaJsonData)
      setTimeout(() => {
        this.loadGeoJsonDataSource(BeiJingJsonData, true, false).then((entities) => {
          let colorHash = {}
          entities.values.map((ele, index) => {
            if (!colorHash[ele.name]) {
              colorHash[ele.name] = Color.fromRandom({ alpha: 1.0 })

            }
            ele.polygon.material = colorHash[ele.name]
            ele.polygon.outline = false
            ele.polygon.extrudedHeight = ele.properties.adcode / 50.0
          })
        })
        console.log('timeout')
      }, 3000)
    },
    loadGeoJsonDataSource(geojson, flyTo = true, only = true) {
      if (!this.customDataSource) {
        this.customDataSource = new CustomDataSource('myData')
        this.viewer.dataSources.add(this.customDataSource)
      }

      return GeoJsonDataSource.load(geojson, {
        stroke: Color.fromAlpha(Color.HOTPINK, 1),
        fill: Color.fromAlpha(Color.PINK, 0.2),
        strokeWidth: 3,
        markerSymbol: '?'
      }).then(dataSource => {
        only && this.customDataSource.entities.removeAll()
        dataSource.entities.values.forEach(entity => {
          this.customDataSource.entities.add(entity)
        })

        flyTo && this.viewer.flyTo(this.customDataSource, {
          duration: 0.8
        })

        return dataSource.entities
      })
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
