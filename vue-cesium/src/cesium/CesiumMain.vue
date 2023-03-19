<script setup lang="ts">
import { reactive, onMounted } from "vue";
import * as Cesium from "cesium";
import "cesium/Build/Cesium/Widgets/widgets.css";
 
const props = defineProps({
  viewStyle: {},
  viewerProperty: {},
  dropBackground: {
    default: false,
  },
});
 
onMounted(() => {
  const _this = this;
  const tianDiTuToken = "4402d5006797897fc5846f4a260540fa";
  // 服务负载子域
  const subdomains = ["0", "1", "2", "3", "4", "5", "6", "7"];
  // 创建图层
  const viewer = new Cesium.Viewer("cesiumContainer", {
    animation: false, //是否创建动画小器件，左下角仪表
    timeline: false, //是否显示时间轴
    geocoder: false, //是否显示geocoder小器件，右上角查询按钮
    baseLayerPicker: false, //是否显示图层选择器
    fullscreenButton: false, //是否显示全屏按钮
    homeButton: true, //是否显示Home按钮
    infoBox: false, //是否显示信息框
    sceneModePicker: false, //是否显示3D/2D选择器
    scene3DOnly: false, //如果设置为true，则所有几何图形以3D模式绘制以节约GPU资源
    selectionIndicator: false, //是否显示选取指示器组件
    navigationHelpButton: false, //是否显示右上角的帮助按钮
    baselLayerPicker: false, // 将图层选择的控件关掉，才能添加其他影像数据
    shadows: true, //是否显示背影
    imageryProvider: new Cesium.WebMapTileServiceImageryProvider({
      // 影像底图
      url: 'http://t0.tianditu.com/img_w/wmts?service=wmts&request=GetTile&version=1.0.0&LAYER=img&tileMatrixSet=w&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}&style=default&format=tiles&tk='+tianDiTuToken,
      layer: "tdtBasicLayer",
      style: "default",
      format: "image/jpeg",
      subdomains: subdomains,
      show: true,
      tileMatrixSetID: "GoogleMapsCompatible",
      maximumLevel: 18,
    }),
  });
  //   将图层挂载到window上
  window.cesiumViewer = viewer;
  console.log(viewer);
  viewer.imageryLayers.addImageryProvider(
    new Cesium.WebMapTileServiceImageryProvider({
      //影像注记
      url: 'http://t0.tianditu.com/cia_w/wmts?service=wmts&request=GetTile&version=1.0.0&LAYER=cia&tileMatrixSet=w&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}&style=default.jpg&tk='+tianDiTuToken,
      subdomains: subdomains,
      layer: "tdtCiaLayer",
      style: "default",
      format: "image/jpeg",
      tileMatrixSetID: "GoogleMapsCompatible",
      maximumLevel: 18,
    })
  );
  //优化项--关闭相关特效
  viewer.scene.debugShowFramesPerSecond = false; //显示fps
  viewer.scene.moon.show = false; //月亮
  viewer.scene.fog.enabled = false; //雾
  viewer.scene.sun.show = false; //太阳
  viewer.scene.skyBox.show = false; //天空盒
  viewer.resolutionScale = 1.0; //画面细度，默认值为1.0
  //去除版权信息
  viewer._cesiumWidget._creditContainer.style.display = "none";
  // 将三维球定位到中国
  viewer.camera.flyTo({
    destination: Cesium.Cartesian3.fromDegrees(103.84, 31.15, 17850000),
    orientation: {
      heading: Cesium.Math.toRadians(348.4202942851978),
      pitch: Cesium.Math.toRadians(-89.74026687972041),
      roll: Cesium.Math.toRadians(0),
    },
    complete: function callback() {
      // 定位完成之后的回调函数
    },
  });
});
</script>

<template>
  <div id="app">
    <div id="cesiumContainer"></div>
  </div>
</template>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>