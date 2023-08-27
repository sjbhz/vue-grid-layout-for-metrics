<template>
  <div ref="MyChartDom" :style="{width: '100%', height: '100%'}"></div>
</template>

<script>
import * as echarts from "echarts"; //引入图标

export default {
  name: "LineChart", //命名组件
  data() {
    return {
      option: {
        //创建折线图的配置项数据
        xAxis: {
          type: "category",
          data: ["Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun"]
        },
        yAxis: {
          type: "value"
        },
        series: [
          {
            data: [150, 230, 224, 218, 135, 147, 260],
            type: "line"
          }
        ]
      },
      ChartDom: null,
      MyChart: null
    };
  },
  methods: {
    CreateChart() {
      this.ChartDom = this.$refs.MyChartDom; // 获取到你防止echart的Dom
      this.MyChart = echarts.init(this.ChartDom); // 实例化你的 echart 图
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
  },
  mounted() {
    // 页面初始化后执行
    this.CreateChart();
  }
};
</script>

<style>
</style>
