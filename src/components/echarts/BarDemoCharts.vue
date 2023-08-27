<template>
  <div ref="MyChartDom" :style="{width: '100%', height: '100%'}"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "BarDemoCharts",
  data() {
    return {
      option: {
        tooltip: {},
        legend: {
          data: ["销量"]
        },
        xAxis: {
          data: ["衬衫", "羊毛衫", "雪纺衫", "裤子", "高跟鞋", "袜子"]
        },
        yAxis: {},
        series: [
          {
            name: "销量",
            type: "bar", //类型为柱状图
            data: [5, 20, 36, 10, 10, 20]
          }
        ]
      },
      ChartDom: null,
      MyChart: null
    };
  },
  props: {},
  mounted() {
    this.initEcharts();
  },
  methods: {
    initEcharts() {
      let ChartDom = this.$refs.MyChartDom; // 获取到你防止echart的Dom
      this.MyChart = echarts.init(ChartDom); // 实例化你的 echart 图
      this.MyChart.setOption(this.option);
      setTimeout(() => {
        //由于网格布局拖拽放大缩小图表不能自适应，这里设置一个定时器使得echart加载为一个异步过程
        this.$nextTick(() => {
          this.MyChart.resize();
        });
      }, 0);
    },
    sizechange() {
      // 修改 echart 大小
      this.MyChart.resize();
    }
  }
};
</script>

<style scoped>
</style>

