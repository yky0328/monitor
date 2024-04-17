<template>
  <div id="cpuChart" style="width: 2000px; height: 600px"></div>
</template>

<script>
import * as echarts from "echarts";

export default {
  name: "CpuChart",
  props: ["starttime", "endtime"],

  data() {
    return {
      myChart: null,
      query_user: 'rate(node_cpu_seconds_total{mode="user",cpu="0", instance="c4"}[1m])',
      query_system: 'rate(node_cpu_seconds_total{mode="system",cpu="0", instance="c4"}[1m])',
      query_iowait: 'rate(node_cpu_seconds_total{mode="iowait",cpu="0", instance="c4"}[1m])',
      query_idle: 'rate(node_cpu_seconds_total{mode="idle",cpu="0", instance="c4"}[1m])',
      query_softirq: 'rate(node_cpu_seconds_total{mode="softirq",cpu="0", instance="c4"}[1m])',

      monitor_data_user: null,
      monitor_data_system: null,
      monitor_data_iowait: null,
      monitor_data_idle: null,
      monitor_data_softirq: null,
    };
  },
  mounted() {
    this.myChart = echarts.init(document.getElementById("cpuChart"));
    this.drawChart([[]],[[]],[[]],[[]],[[]]);
  },

  watch: {
    starttime: async function (val) {
      await this.confirm();
      this.drawChart(this.monitor_data_user, this.monitor_data_system, this.monitor_data_iowait, this.monitor_data_idle, this.monitor_data_softirq);
    },
    endtime: async function (val) {
      await this.confirm();
      this.drawChart(this.monitor_data_user, this.monitor_data_system, this.monitor_data_iowait, this.monitor_data_idle, this.monitor_data_softirq);
    },
  },
  methods: {

    async confirm() {

      var params_user = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_user,
      };

      var params_system = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_system,
      };

      var params_idle = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_idle,
      };

      var params_iowait = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_iowait,
      };

      var params_softirq = {
        starttime: this.starttime / 1000,
        endtime: this.endtime / 1000,
        query: this.query_softirq,
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
          params_user
        );
        this.monitor_data_user = res.data[0].values;
        console.log("in confirm : user", this.monitor_data_user);

        res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params_system
        );
        this.monitor_data_system = res.data[0].values;
        
        res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params_iowait
        );
        this.monitor_data_iowait = res.data[0].values;

        res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params_idle
        );
        this.monitor_data_idle = res.data[0].values;

        res = await this.$axios.post(
          "http://127.0.0.1:5000/get_data",
          params_softirq
        );
        this.monitor_data_softirq = res.data[0].values;

      } catch (error) {
        console.log(error);
      }
    },


    drawChart(monitor_data_user, monitor_data_system, monitor_data_iowait, monitor_data_idle, monitor_data_softirq) {
      // const chartDom = document.getElementById("cpuChart");
      // const myChart = echarts.init(chartDom);

      var values1 = [[]];
      var values2 = [[]]; 
      var values3 = [[]];
      var values4 = [[]];
      var values5 = [[]];
      if (monitor_data_user != null) {
        values1 = monitor_data_user;
      }
      if (monitor_data_system != null) {
        values2 = monitor_data_system;
      }
      if (monitor_data_iowait != null) {
        values3 = monitor_data_iowait;
      }
      if (monitor_data_idle != null) {
        values4 = monitor_data_idle;
      }
      if (monitor_data_softirq != null) {
        values5 = monitor_data_softirq;
      }

      // 数据转换函数
      const processData = (values) =>
        values.map(value => parseFloat(value[1]));

      const option = {
        title: {
          text: "CPU Chart",
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "cross",
            crossStyle: {
              color: "#999",
            },
          },
        },
        legend: {
          data: ["user", "system", "iowait", "idle", "softirq"],
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
          type: "value",
        },
        series: [
          {
            name: "user",
            type: "line",
            data: processData(values1),
          },
          {
            name: "system",
            type: "line",
            data: processData(values2),
          },
          {
            name: "iowait",
            type: "line",
            data: processData(values3),
          },
          {
            name: "idle",
            type: "line",
            data: processData(values4),
          },
          {
            name: "softirq",
            type: "line",
            data: processData(values5),
          },
        ],
      };
      option.xAxis.data = values1.map(value => value[0]);
      this.myChart.setOption(option);
      this.$emit("update", option);
    },
  },
};
</script>
