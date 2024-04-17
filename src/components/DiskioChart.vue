<template>
  <div id="diskio" style="width: 1000px;height:400px;"></div>
</template>

<script>
import * as echarts from 'echarts';

export default {
  name: 'LineChart',
  props: ["starttime", "endtime"],

  data() {
    return {
      myChart: null,
      query_a:
        'rate(node_disk_io_now{device="vda", instance="c4"}[1m])',
      query_b:'rate(node_disk_io_now{device="vdb", instance="c4"}[1m])',
      monitor_data_a: null,
      monitor_data_b: null,
    };
  },
  watch: {
    starttime: async function (val) {
      await this.confirm();
      this.drawChart(this.monitor_data_a, this.monitor_data_b);
    },
    endtime: async function (val) {
      await this.confirm();
      this.drawChart(this.monitor_data_a, this.monitor_data_b);
    },
  },

  mounted() {
    this.myChart = echarts.init(document.getElementById("diskio"));
    this.drawChart([[]],[[]]);
  },
  methods: {
    async confirm() {

      var params_a = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_a,
      };

      var params_b = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_b,
      };

      if (
        this.starttime === "" ||
        this.endtime === "" ||
        this.starttime >= this.endtime ||
        this.query_a === "" ||
        this.query_b === ""
      ) {
        console.log("请选择时间或者输入查询语句");
        return;
      }

      try {
        var res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params_a
        );
        this.monitor_data_a = res.data[0].values;

        res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params_b
        );
        this.monitor_data_b = res.data[0].values;


      } catch (error) {
        console.log(error);
      }
    },


    drawChart(tmpdata1, tmpdata2) {
      // const chartDom = document.getElementById('diskio');
      // const myChart = echarts.init(chartDom);
      const option = {
        title: {
          text: 'node_disk_io_now'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#6a7985'
            }
          }
        },
        xAxis: {
          type: 'category',
          boundaryGap: false,
          data: [], // X轴时间戳数据将在下面填充
          axisLabel: {
            formatter: function (value) {
              // 将时间戳转换为更易读的格式，例如 'YYYY-MM-DD HH:mm:ss'
              const date = new Date(value * 1000);
              return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}:${date.getSeconds()}`;
            }
          }
        },
        yAxis: {
          type: 'value'
        },
        series: [
          {
            name: 'disk_io_device=vda',
            type: 'line',
            data: [] // Y轴数值数据将在下面填充
          },
          {
            name: 'disk_io_device=vdb',
            type: 'line',
            data: [] // 第二条折线的数据，将在下面填充
          }
        ]
      };

      // 第一条折线数据
      var values1 = [[]];
      // 填充数据
      if (tmpdata1 != null) {
        values1 = tmpdata1;
      }
      option.xAxis.data = values1.map(value => value[0]);
      option.series[0].data = values1.map(value => parseFloat(value[1]));

      // 第二条折线数据
      var values2 = [[]];
      // 填充数据
      if (tmpdata2 != null) {
        values2 = tmpdata2;
      }
      // 由于第二条线可能有不同的时间戳，可以考虑合并两个时间戳数组并去重，如果需要
      // 这里我们直接使用第二条线的时间戳更新xAxis，仅作为示例
      option.xAxis.data = values2.map(value => value[0]);
      option.series[1].data = values2.map(value => parseFloat(value[1]));

      // 使用刚指定的配置项和数据显示图表。
      this.myChart.setOption(option);
      this.$emit("update", option);
    }
  }
}
</script>

<style>
/* 你可以在这里添加一些样式 */
</style>
