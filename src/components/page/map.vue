<template>
  <div class="mapbox">
    <baidu-map :center="center" :zoom="zoom" :scroll-wheel-zoom="true" style="height:100vh" @ready="handler" @click="getClickInfo" >
      <!-- 必须给容器指高度，不然地图将显示在一个高度为0的容器中，看不到 -->
      <bm-navigation anchor="BMAP_ANCHOR_TOP_RIGHT"></bm-navigation>
      <bm-geolocation anchor="BMAP_ANCHOR_BOTTOM_RIGHT" :showAddressBar="true" :autoLocation="true"></bm-geolocation>
      <bm-city-list anchor="BMAP_ANCHOR_TOP_LEFT"></bm-city-list>
       <bm-marker :position="center" :dragging="true" @click="infoWindowOpen" animation="BMAP_ANIMATION_BOUNCE" :icon="{url: 'https://i.loli.net/2021/01/24/pEyfV5mr2MkxzqC.png', size: {width:50, height:50}}">
        <!-- <bm-label content="三官堂大桥" :labelStyle="{color: 'red', fontSize : '24px'}" :offset="{width: -35, height: 30}"/> -->
        <bm-info-window :show="show" @close="infoWindowClose" @open="infoWindowOpen" :offset="{width: 35, height: -30}"   > 
            <div>
                <h4 style='margin:0 0 5px 0;'>{{title}}</h4>
    <img style='float:right;margin:0 4px 22px' id='imgDemo' :src="bridgeimg" width='139' height='104'/>
    <p style='margin:0;line-height:1.5;font-size:13px;text-indent:2em'>
    {{bridgecontend}}
    </p> 
    <a href="https://sm.ms/image/UuCr27X4stBigTp" target="_blank">查看三维模型</a>
     
    	
    </div> 
        </bm-info-window>
       </bm-marker>
  </baidu-map>
    
    <!-- 因为我采用的是全局注册，所以不用再在该页面上注册components -->
  </div>
</template>
<script>
export default {
  name: "mapbox",
  data() {
    return {
      center: { lng: 0, lat: 0 }, //经纬度
      zoom: 14 ,//地图展示级别 比例尺
      show: false,
      title:"桥的名字",
      bridgeimg:'https://i.loli.net/2021/01/24/b8tzyfpEJS5idjv.png',
      bridgecontend:"天安门坐落在中国北京市中心,故宫的南侧,与天安门广场隔长安街相望,是清朝皇城的大门...",
      
    };
  },
  methods: {
      infoWindowClose () {
      this.show = false
    },
    infoWindowOpen () {
      this.show = true
    },
    handler({ BMap, map }) {
      console.log(BMap, map);
      this.center.lng = 121.6281;
      this.center.lat =29.91053;
      this.zoom = this.zoom;
    },
    getClickInfo(e) {
      console.log(e.point.lng);
      console.log(e.point.lat);
      this.center.lng = e.point.lng;
      this.center.lat = e.point.lat;
    }
  }
};
</script>
