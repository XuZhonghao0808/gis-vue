<script setup lang="ts">
import { reactive, onMounted } from "vue";
import * as Cesium from "cesium";
import "cesium/Build/Cesium/Widgets/widgets.css";
import { tokTypes } from "@babel/parser";
 
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
  const TDT_URL = 'http://t{s}.tianditu.gov.cn/';
  // 服务负载子域
  const subdomains = ["0", "1", "2", "3", "4", "5", "6", "7"];
  //c：经纬度投影;
  const TDT_MATRIX_C = "c"
  // w：球面墨卡托投影
  const TDT_MATRIX_W = "w"
  const TDT_LAYER = '/wmts?LAYER='
  const TDT_MATRIX_SET = '&tileMatrixSet='
  const TDT_PARAMETER = '&service=wmts&request=GetTile&version=1.0.0&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}&style=default&format=tiles&tk='+tianDiTuToken;
  //影像地图
  var TDT_IMG = 'img'
  //影像中文标注
  var TDT_CIA = 'cia';
  //矢量地图
  var TDT_VEC = 'vec';
  //矢量中文标注
  var TDT_CVA = 'cva';

  // 创建图层
  const viewer = new Cesium.Viewer("cesiumContainer", {
    animation: false, //是否创建动画小器件，左下角仪表
    timeline: false, //是否显示时间轴
    geocoder: false, //是否显示geocoder小器件，右上角查询按钮
    baseLayerPicker: false, //是否显示图层选择器
    fullscreenButton: true, //是否显示全屏按钮
    homeButton: false, //是否显示Home按钮
    infoBox: true, //是否显示信息框
    sceneModePicker: true, //是否显示3D/2D选择器
    scene3DOnly: false, //如果设置为true，则所有几何图形以3D模式绘制以节约GPU资源
    selectionIndicator: true, //是否显示选取指示器组件
    navigationHelpButton: true, //是否显示右上角的帮助按钮
    baselLayerPicker: false, // 将图层选择的控件关掉，才能添加其他影像数据
    shadows: true, //是否显示背影
    imageryProvider: new Cesium.WebMapTileServiceImageryProvider({
      // 矢量底图
      url: TDT_URL + TDT_IMG +"_"+ TDT_MATRIX_C + TDT_LAYER + TDT_IMG + TDT_MATRIX_SET + TDT_MATRIX_C + TDT_PARAMETER,
      layer: "tdtVecLayer",
      style: "default",
      format: "tiles",
      subdomains:subdomains,
      tileMatrixSetID: "EPSG:4326",
    }),
  });
  //将图层挂载到window上
  window.cesiumViewer = viewer;
  viewer.imageryLayers.addImageryProvider(
    new Cesium.WebMapTileServiceImageryProvider({
      //标注、路网
      url: TDT_URL + TDT_CVA +"_"+ TDT_MATRIX_C + TDT_LAYER + TDT_CVA + TDT_MATRIX_SET + TDT_MATRIX_C + TDT_PARAMETER,
      layer: "tdtCiaLayer",
      style: "default",
      format: "tiles",
      subdomains: subdomains,
      tileMatrixSetID: "EPSG:4326",
    })
  );
  // viewer.imageryLayers.addImageryProvider(
  //   new Cesium.WebMapTileServiceImageryProvider({
  //     url:"http://t{s}.tianditu.gov.cn/vec_w/wmts?service=wmts&request=GetTile&version=1.0.0&LAYER=vec&tileMatrixSet=w&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}&style=default&format=tiles&tk=自己的申请的tk"+tokTypes,
  //     layer: "tdtVecLayer",
  //     style: "default",
  //     format: "tiles",
  //     tileMatrixSetID: "GoogleMapsCompatible",
  //   })
  // );
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
#app,#cesiumContainer{
  position: fixed;
  left: 0;
  top: 0;
  width: 100vw;
}
</style>