<template>
  <div id="rtchart" style="width: 1000px; height: 400px"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "LineChart",
  mounted() {
    this.drawChart();
  },
  methods: {
    drawChart() {
      const chartDom = document.getElementById("rtchart");
      const myChart = echarts.init(chartDom);
      const option = {
        title: {
          text: "response time avg",
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            label: {
              backgroundColor: "#6a7985",
            },
          },
        },
        xAxis: {
          type: "category",
          boundaryGap: false,
          data: [], // X轴时间戳数据将在下面填充
          axisLabel: {
            formatter: function (value) {
              // 将时间戳转换为更易读的格式，例如 'YYYY-MM-DD HH:mm:ss'
              const date = new Date(value * 1000);
              return `${date.getFullYear()}-${
                date.getMonth() + 1
              }-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`;
            },
          },
        },
        yAxis: {
          type: "value",
        },
        series: [
          {
            name: "rt",
            type: "line",
            stack: "总量",
            areaStyle: {},
            data: [], // Y轴数值数据将在下面填充
          },
        ],
      };

      // 填充数据
      const values = [
        [1712651355, "21.9"],
        [1712651360, "41.7"],
        [1712651365, "50.8"],
        [1712651370, "83.9"],
        [1712651375, "81.2"],
        [1712651380, "73.9"],
        [1712651385, "74.6"],
        [1712651390, "84.1"],
        [1712651395, "89.6"],
        [1712651400, "83.5"],
       
      ];
      option.xAxis.data = values.map((value) => value[0]);
      option.series[0].data = values.map((value) => parseFloat(value[1]));
      // 使用刚指定的配置项和数据显示图表。
      myChart.setOption(option);
    },
  },
};
</script>

<style>
/* 你可以在这里添加一些样式 */
</style>