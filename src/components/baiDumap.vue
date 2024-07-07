<template>
  <!-- 百度地图  -->
  <div id="bai-du-map">
    <!-- 技术支持和联系方式  -->
    <el-dialog :visible.sync="dialogVisible" width="1600">
      <div class="textbox" style="background-color: #3399ff">
        <span class="text" v-for="(item, index) in date" :key="index">{{
          item
        }}</span>
      </div>
      <div class="textbox" style="background-color: #ff9900">
        <span class="text" v-for="(item, index) in tempMax" :key="index">{{
          item
        }}</span>
      </div>
      <div class="textbox" style="background-color: #ffcc66">
        <span class="text" v-for="(item, index) in tempMin" :key="index">
          {{ item }}</span
        >
      </div>
      <div class="textbox" style="background-color: #33ff33">
        <span class="text" v-for="(item, index) in weather" :key="index">{{
          item
        }}</span>
      </div>
      <template #footer> </template>
    </el-dialog>
  </div>
</template>

<script>
import AMapLoader from "@amap/amap-jsapi-loader";
import axios from "axios";
// 设置安全密钥
window._AMapSecurityConfig = {
  securityJsCode: "4256c3b5db16f828b7ebf8e53954ee9b",
};
export default {
  data() {
    return {
      map: null,
      mouseTool: null,
      overlays: [],
      auto: null,
      placeSearch: null,
      dialogVisible: false,
      date: [],
      tempMax: [],
      tempMin: [],
      weather: [],
    };
  },
  mounted() {
    this.initMap();
  },

  methods: {
    initMap() {
      AMapLoader.load({
        key: "e96d7a39f2e10c79f5910e85e99bc7d3",
        version: "2.0", // 指定要加载的 JSAPI 的版本，缺省时默认为 1.4.15
        // 需要使用的的插件列表，如比例尺'AMap.Scale'等
      })
        .then((AMap) => {
          // 初始化地图
          var map = new AMap.Map("bai-du-map", {
            viewMode: "2D", //  是否为3D地图模式
            zoom: 5, // 初始化地图级别
            center: [116.397428, 39.90923],
            resizeEnable: true,
          });
          let that = this;
          //获取省会城市位置
          var cities = [
            "北京",
            "上海",
            "⼴州",
            "成都",
            "杭州",
            "武汉",
            "南京",
            "西安",
            "沈阳",
            "⻓春",
            "济南",
          ];
          cities.forEach(function (city) {
            AMap.plugin("AMap.Geocoder", function () {
              var geocoder = new AMap.Geocoder();
              geocoder.getLocation(city, function (status, result) {
                // console.log(status, result);
                if (status === "complete" && result.geocodes.length) {
                  var position = result.geocodes[0].location;
                  // var adcode = result.geocodes[0].adcode;
                  var marker = new AMap.Marker({
                    position: position,
                    extData: position,
                  });
                  map.add(marker),
                    // map.setCenter(position);
                    marker.on("click", (e) => {
                      that.date = [];
                      that.tempMax = [];
                      that.tempMin = [];
                      that.weather = [];
                      axios
                        .get(
                          `/api/v7/weather/7d?location=${position.lng.toFixed(
                            2
                          )},${position.lat.toFixed(
                            2
                          )}&key=b9aa32f2f3124f86a12b81b30a60b58a`
                        )
                        .then((res) => {
                          console.log(res);
                          console.log(res.data.daily);
                          res.data.daily.forEach((item) => {
                            that.date.push(item.fxDate);
                            that.tempMax.push(item.tempMax);
                            that.tempMin.push(item.tempMin);
                            that.weather.push(item.textDay);
                            // console.log(item.fxDate);
                          });

                          that.dialogVisible = true;
                        });
                    });
                }
              });
            });
          });
        })
        .catch((e) => {
          console.log(e);
        });
    },
  },
};
</script>

<style scoped>
#bai-du-map {
  overflow: hidden;
  width: 100%;
  height: 100%;
  margin: 0;
  font-family: "微软雅黑";
}
</style>
<style>
/* 隐藏高德logo  */
.amap-logo {
  display: none !important;
}
/* 隐藏高德版权  */
.amap-copyright {
  display: none !important;
}

.el-dialog__header {
  padding: 0 !important;
}
.el-dialog {
  width: 70% !important;
}
.textbox {
  display: flex;
  flex-direction: row;
  justify-content: space-around;
}
.text {
  margin: 15px 15px;
  width: 80px;
}
</style>
