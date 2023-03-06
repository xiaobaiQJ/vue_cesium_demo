<template>
  <div v-if="className" :class="className">
    <div :id="id" ref="cesiumContainer" :style="{ height: height, width: width }" />
  </div>
  <div v-else :id="id" ref="cesiumContainer" :style="{ height: height, width: width }" />
</template>

<script>
import 'cesium/Build/Cesium/Widgets/widgets.css'
import {
  Viewer,
  GeoJsonDataSource,
  Color,
  CustomDataSource,
  HeadingPitchRange,
  Math as CesiumMath
} from 'cesium'

import china from './100000_full.json'
import beijiing from './110000_full.json'

export default {
  name: 'CesiumMap',
  props: {
    instance: {
      type: Object,
      default: () => undefined
    },
    className: {
      type: String,
      default: ''
    },
    id: {
      type: String,
      default: (() => Math.random().toString(36).substr(2))()
    },
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
      const el = this.$refs.cesiumContainer

      const viewer = this.viewer = new Viewer(el, {
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

      viewer.scene.globe.baseColor = Color.BLACK // 设置地球颜色
      // 去除logo
      this.viewer.cesiumWidget.creditContainer.style.display = "none";
      this.$emit('update:instance', viewer)

      this.loadGeoJsonDataSource(china)
      setTimeout(() => {
        this.loadGeoJsonDataSource(beijiing, true, false).then(function (entities) {
          entities = entities.values

          const colorHash = {}
          for (let i = 0; i < entities.length; i++) {
            const entity = entities[i]
            const name = entity.name
            let color = colorHash[name]
            if (!color) {
              color = Color.fromRandom({ alpha: 1.0 })
              colorHash[name] = color
            }

            entity.polygon.material = color
            entity.polygon.outline = false

            entity.polygon.extrudedHeight = entity.properties.adcode / 50.0
          }
        })
        console.log('timeout')
      }, 3000)
    },
    loadGeoJsonDataSource(geojson, flyTo = true, only = true) {
      const viewer = this.viewer
      let customDataSource = this.customDataSource
      if (!customDataSource) {
        this.customDataSource = customDataSource = new CustomDataSource('myData')
        viewer.dataSources.add(customDataSource)
      }

      return GeoJsonDataSource.load(geojson, {
        stroke: Color.fromAlpha(Color.HOTPINK, 1),
        fill: Color.fromAlpha(Color.PINK, 0.2),
        strokeWidth: 3,
        markerSymbol: '?'
      }).then(dataSource => {
        only && customDataSource.entities.removeAll()
        dataSource.entities.values.forEach(entity => {
          customDataSource.entities.add(entity)
        })

        flyTo && viewer.flyTo(customDataSource, {
          duration: 0.8
        })

        return dataSource.entities
      })
    }
  }
}
</script>
