<template>
  <div id="mem" style="width: 1000px; height: 400px"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "LineChart",
  props: ["starttime", "endtime"],

  data() {
    return {
      myChart: null,
      query:
        'rate(node_memory_MemAvailable_bytes{instance="c4"}[1m])',
      monitor_data: null,
    };
  },
  watch: {
    starttime: async function (val) {
      await this.confirm();
      this.drawChart(this.monitor_data);
    },
    endtime: async function (val) {
      await this.confirm();
      this.drawChart(this.monitor_data);
    },
  },

  mounted() {
    this.myChart = echarts.init(document.getElementById("mem"));
    this.drawChart([[]]);
  },

  methods: {

    async confirm() {

      var params = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query,
      };

      if (
        this.starttime === "" ||
        this.endtime === "" ||
        this.starttime >= this.endtime ||
        this.query === ""
      ) {
        console.log("请选择时间或者输入查询语句");
        return;
      }

      try {
        const res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params
        );
        this.monitor_data = res.data[0].values;
      } catch (error) {
        console.log(error);
      }
    },


    drawChart(tmpdata) {
      // const chartDom = document.getElementById("mem");
      // const myChart = echarts.init(chartDom);
      const option = {
        title: {
          text: "memory_available",
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
            name: "mem",
            type: "line",
            stack: "总量",
            areaStyle: {},
            data: [], // Y轴数值数据将在下面填充
          },
        ],
      };

      // 填充数据
      var values = [[]];
      if (tmpdata != null) {
        values = tmpdata;
      }

      option.xAxis.data = values.map((value) => value[0]);
      option.series[0].data = values.map((value) => parseFloat(value[1]));
      // 使用刚指定的配置项和数据显示图表。
      this.myChart.setOption(option);
      this.$emit("update", option);
    },
  },
};
</script>

<style>
/* 你可以在这里添加一些样式 */
</style>