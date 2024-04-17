<template>
  <div
    class="echart"
    :style="{ float: 'left', width: '30%', height: '400px' }"
    ref="chart"
  ></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  mounted() {
    this.initEcharts();
  },
  methods: {
    initEcharts() {
      const values = [
        [1711957500, "33600.955555555556"],
        [1711957530, "34333.688888888886"],
        [1711957560, "33469.48888888889"],
        [1711957590, "33214.688888888886"],
        [1711957620, "33260.666666666664"],
        [1711957650, "33432.2"],
        [1711957680, "33831.177777777775"],
        [1711957710, "33459.2"],
        [1711957740, "33300.97777777778"],
        [1711957770, "34314.911111111105"],
        [1711957800, "34114.777777777774"],
      ];

      const option = {
        title: {
          text: "折线图示例",
        },
        tooltip: {
          trigger: 'axis',
          formatter: '{b}: {c}',
        },
        xAxis: {
          type: "time",
          data: values.map(item => item[0]),
        },
        yAxis: {
          type: "value",
          min: 30000,
          max: 40000,
        },
        series: [
          {
            name: "数据",
            type: "line",
            data: values.map(item => [item[0], parseFloat(item[1])]),
          },
        ],
      };

      const myChart = echarts.init(this.$refs.chart);
      myChart.setOption(option);

      // 监听窗口大小变化，调整图表尺寸
      window.addEventListener("resize", () => {
        myChart.resize();
      });
    },
  },
};
</script>

<style scoped>
/* 在这里可以添加样式 */
</style>
