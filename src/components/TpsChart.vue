<template>
  <div id="charttps" style="width: 1000px; height: 400px"></div>
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
      const chartDom = document.getElementById("charttps");
      const myChart = echarts.init(chartDom);
      const option = {
        title: {
          text: "tps",
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
            name: "tps",
            type: "line",
            stack: "总量",
            areaStyle: {},
            data: [], // Y轴数值数据将在下面填充
          },
        ],
      };

      // 填充数据
      const values = [
        [1712651355, "290"],
        [1712651360, "628"],
        [1712651365, "734"],
        [1712651370, "744"],
        [1712651375, "801"],
        [1712651380, "737"],
        [1712651385, "823"],
        [1712651390, "796"],
        [1712651395, "831"],
        [1712651400, "784"],
       
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