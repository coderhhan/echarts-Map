<template>
  <div id="app">
    <div class="data-container">
      <div class="title">全国疫情 <h3>{{allData.times}}</h3></div>
      <div class="items">
       <div class="item">
         <h4 class="text">
           现存确诊</h4>
         <span class="text">{{allData.econNum}}</span>
         <h5 class="text">较比昨日<code>{{addData.addecon_new}}</code></h5>
       </div>
       <div class="item">
         <h4 class="text">现存确诊重症</h4>
         <span class="text">{{allData.heconNum}}</span>
         <h5 class="text">较比昨日<code>{{addData.addhecon_new}}</code></h5>
       </div>
       <div class="item">
         <h4 class="text">现存疑似</h4>
         <span class="text">{{allData.sustotal}}</span>
         <h5 class="text">较比昨日<code>{{addData.wjw_addsus_new}}</code></h5>
       </div>
       <div class="item">
         <h4 class="text">累计确诊</h4>
         <span class="text">{{allData.gntotal}}</span>
         <h5 class="text">较比昨日<code>{{addData.addcon_new}}</code></h5>
       </div>
       <div class="item">
         <h4 class="text">累计死亡</h4>
         <span class="text">  {{allData.deathtotal}}</span>
         <h5  class="text">较比昨日<code>{{addData.adddeath_new}}</code></h5>
       </div>
       <div class="item">
         <h4 class="text">累计治愈</h4>
         <span class="text">{{allData.curetotal}}</span>
         <h5 class="text">较比昨日<code>{{addData.addcure_new}}</code></h5>
       </div>
     </div>
    </div>
    <div ref="mapBox" style="height:600px;margin: 0 auto;padding:0 1.6rem"></div>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import echarts from "echarts";
import "echarts/map/js/china";

import jsonp from "jsonp";
const option = {
  title: {
    text: "新型冠状病毒分布图",
    align: "center",
    x: 'center'
  },
  series: [
    {

      name: "确诊人数", //系列名称，用于tooltip的显示，legend 的图例筛选，在 setOption 更新数据和配置项时用于指定对应的系列。
      type: "map",
      map: "china", //渲染中国地图
      label: {  //图形上的文本标签，可用于说明图形的一些数据信息，比如值，名称等，
        //控制对应地区的汉字
        show: true,
        color: "#333333",
        fontSize: 10
      },
      zoom: 1.2, //控制地图的放大和缩小
      itemStyle: {
        //控制地图板块的颜色和边框
        areaColor: "#ffffff"
        //borderColor:"blue" //地图边框颜色
      },
      emphasis: {
        //控制鼠标滑过之后的背景色和字体颜色
        label: {
          color: "#000000",
          fontSize: 11
        },
        itemStyle: {
          areaColor: "#FFFB85"
        }
      },

      data: [] //用来展示后台给的数据
    }
  ],
  visualMap: [  //ECharts 设置地图(map)值变化颜色(visualMap):
    {
      type: "piecewise",
      show: true,
      itemWidth:20,
      pieces: [
        //分段
        { min: 1000 },
        { min: 1000, max: 9999 },
        { min: 100, max: 999 },
        { min: 10, max: 99 },
        { min: 1, max: 9 },
        { min: 0, max: 0 }
      ],
      // itemSymbol:"roundRect",
      //showLabel:false, // 控制分段的字的显示
      // 值域
      inRange: {
        borderColor: '#2B2B2B',
        color: ["#FFFFFF", "#4F080E"]
      },
      // zlevel:10,
      // z:10,
      // x:"center",
      // y:"bottom",
      //
      orient:"horizontal",
      // padding:[10, 30,0,0],
    }
  ],
  // 值域详细设置
  // dataRange: {
  //   orient: 'vertical',        // 布局方式，默认为垂直布局，可选为：
  //                              // 'horizontal' ¦ 'vertical'
  //   x: 'left',                 // 水平安放位置，默认为全图左对齐，可选为：
  //                              // 'center' ¦ 'left' ¦ 'right'
  //                              // ¦ {number}（x坐标，单位px）
  //   y: 'bottom',               // 垂直安放位置，默认为全图底部，可选为：
  //                              // 'top' ¦ 'bottom' ¦ 'center'
  //                              // ¦ {number}（y坐标，单位px）
  //   backgroundColor: 'rgba(0,0,0,0)',
  //   borderColor: '#2B2B2B',       // 值域边框颜色
  //   borderWidth: 0,            // 值域边框线宽，单位px，默认为0（无边框）
  //   padding: 5,                // 值域内边距，单位px，默认各方向内边距为5，
  //                              // 接受数组分别设定上右下左边距，同css
  //   itemGap: 10,               // 各个item之间的间隔，单位px，默认为10，
  //                              // 横向布局时为水平间隔，纵向布局时为纵向间隔
  //   itemWidth: 20,             // 值域图形宽度，线性渐变水平布局宽度为该值 * 10
  //   itemHeight: 14,            // 值域图形高度，线性渐变垂直布局高度为该值 * 10
  //   splitNumber: 5,            // 分割段数，默认为5，为0时为线性渐变
  //   color: ["#4F080E","#FFFFFF"],//颜色
  //   //text:['高','低'],         // 文本，默认为数值文本
  //   textStyle: {
  //     color: '#333'          // 值域文字颜色
  //   }
  // },
  tooltip: {
    trugger: "item",
    //提示框浮层内容格式器，支持字符串模板和回调函数两种形式,模板变量有 {a}, {b}，{c}，{d}，{e}，分别表示系列名，数据名，数据值等
    formatter: function(params) {
      // console.log(params) //  现存确诊病例:${params.data.value}<br/>
      return params.data
              ? `
               ${params.data.name}<br/>
              现存确诊病例:${params.data.existNum}<br/>
              确诊病例:${params.data.totalNum}<br/>
              治愈病例:${params.data.cureNum}<br/>
              死亡病例:${params.data.deathNum}<br/>
                        `
              : "";
    }
  }
};
export default {
  name: 'App',
  data() {
  return {
    allData: {},
    addData: {}
  };
},
mounted() {
  this.getData(); //为什么不在created的钩子函数执行，
  this.myChart = echarts.init(this.$refs.mapBox);
  this.myChart.setOption(option, (window.onresize = this.myChart.resize));
},
methods: {
  getData() {
    jsonp(
            "https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427",
            {},
            (err, data) => {
              // console.log(data);
              this.allData = data.data
              if (!err) {
                let list = data.data.list.map(item => ({
                  name: item.name, //省份名称
                  value: item.value-item.cureNum-item.deathNum , //现存数量 //根据现存渲染
                  cureNum: item.cureNum, //疑似数量
                  deathNum: item.deathNum, //死亡数量
                  existNum:item.value-item.cureNum-item.deathNum, //现存数量
                  totalNum:item.value //确诊人数
                }));
                option.series[0].data = list;
                this.addData = data.data.add_daily
                console.log(this.addData);
                this.myChart.setOption(option);
              }
            }
    );
  }
}
};
</script>

<style>
@import "assets/reset.css";
*{
  background-color: #F8F8F8;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;

}
.title{
margin-bottom: 20px;
}
.data-container{
    margin: auto;
    margin-bottom: 10px;
    width: 600px;
  }
.items {
    padding-top: 20px;
    display: flex;
    justify-content: center;
    align-content: center;
    flex-wrap: wrap;
    background-color: white;
  }
.item {
  display: flex;
  justify-content: center;
  align-content: space-evenly;
  flex-wrap: wrap;
  height: 100px;
  width: 200px;
  font-size: 12px;
  background-color: white;
}
.item .text {
    width: 200px;
    background-color: white;
  }
span {
    font-weight:bolder ;
    font-size: 26px;

  }
h4{
  font-weight:bolder ;
}
h3{
  padding-top: 10px;
  font-size: 24px;

}
h5 {
    color: #9999;
    font-family: sans-serif;
  }
h5 code {
    color: #666;
    background-color: white;
    font-family: sans-serif;
  }
.items .item:nth-of-type(1) span {
    color:#ff3535;
  }
.items .item:nth-of-type(2) span {
  color:#8a121c;
}
.items .item:nth-of-type(3) span {
  color:#a36fff;
}
.items .item:nth-of-type(4) span {
  color:#b10000;
}
.items .item:nth-of-type(5) span {
   color:#4b4b4b;
 }
.items .item:nth-of-type(6) span {
  color:#13B593;
}

</style>
