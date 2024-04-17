<template>
  <v-row style="display: flex">
    <!-- <h1>Selected Lines</h1> -->
    <div ref="chartContainer" style="width: 1000px; height: 400px"></div>
  </v-row>

</template>

<script>
import * as echarts from "echarts";

export default {
  name: "LineChart",
  props: {
    option: {
      type: Object,
      default: function(){
        return {
          title: {
            text: "all",
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
          series: [{}],
        };
      },
      },
  },

  data() {
    return {
      myChart: null,
    };
  },
  watch: {

    'option.series':{
      handler: function (val, oldVal) {
        console.log("option changed", val);
        this.drawChart();
      },
      deep: true,
    },

  },

  mounted() {
    this.option = {
      title: {
        text: "all",
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
      series: [2,3,4],
    };
    this.myChart = echarts.init(this.$refs.chartContainer);
    console.log(this.option.title.text);
    this.drawChart(this.option);
  },
  methods: {
    drawChart() {
      try {
        this.myChart.setOption(this.option);
      } catch (error) {
        console.log('error');
        console.log(error);
      }
      // this.myChart.setOption(option);
    },
  },
};
</script>

<style>
/* 你可以在这里添加一些样式 */
</style>