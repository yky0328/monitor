<template>
  <v-row id="app">
    <v-row>
      <v-col cols="12">
        <h1>Dashboard</h1>
      </v-col>
    </v-row>

    <v-row>
      <v-col>
        <span class="demonstration">起始时间</span>
        <el-date-picker
          v-model="starttime"
          type="datetime"
          placeholder="选择日期时间"
          value-format="timestamp"
        >
        </el-date-picker>
      </v-col>

      <v-col>
        <span class="demonstration">结束时间</span>
        <el-date-picker
          v-model="endtime"
          type="datetime"
          placeholder="选择日期时间"
          align="right"
          value-format="timestamp"
        >
        </el-date-picker>
      </v-col>
      <el-button @click="confirm">确定</el-button>
    </v-row>

    <div> <selected-lines :option="lines_option"/></div>
    <v-row style="display: flex">
      <v-col cols="6">
        <tps-chart />
      </v-col>

      <v-col cols="6">
        <rt-chart />
      </v-col>
    </v-row>
    <div id="cpu"><cpu-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="cpu_update"/> </div>
    <v-row style="display: flex">
      <v-col cols="6">
        <context-switch-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="cs_update"/>
      </v-col>

      <v-col cols="6">
        <intr-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="intr_update"/>
      </v-col>
    </v-row>

    <v-row style="display: flex">
      <v-col cols="6">
        <diskio-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="disk_io_update"/>
      </v-col>

      <v-col cols="6">
        <mem-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="mem_update"/>
      </v-col>
    </v-row>

    <v-row style="display: flex">
      <v-col cols="6">
        <disk-read-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="disk_read_update"/>
      </v-col>

      <v-col cols="6">
        <disk-written-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="disk_written_update"/>
      </v-col>
    </v-row>

    <v-row style="display: flex">
      <v-col cols="6">
        <pgfault-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="pgfault_update" />
      </v-col>

      <v-col cols="6">
        <pgmajfault-chart :starttime="starttimeprops" :endtime="endtimeprops" @update="pgmajfault_update"/>
      </v-col>
    </v-row>


  </v-row>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import TestChart from "./components/TestChart.vue";
import IntrChart from "./components/IntrChart.vue";
import CpuChart from "./components/CpuChart.vue";
import TpsChart from "./components/TpsChart.vue";
import RtChart from "./components/RtChart.vue";
import ContextSwitchChart from "./components/ContextSwitchChart.vue";
import PgfaultChart from "./components/PgfaultChart.vue";
import PgmajfaultChart from "./components/PgmajfaultChart.vue";
import DiskioChart from "./components/DiskioChart.vue";
import MemChart from "./components/MemChart.vue";
import DiskReadChart from "./components/DiskReadChart.vue";
import DiskWrittenChart from "./components/DiskWrittenChart.vue";
import SelectedLines from "./components/SelectedLines.vue";

export default {
  name: "App",
  components: {
    // HelloWorld
    TpsChart,
    IntrChart,
    CpuChart,
    RtChart,
    ContextSwitchChart,
    PgfaultChart,
    PgmajfaultChart,
    DiskioChart,
    MemChart,
    DiskReadChart,
    DiskWrittenChart,
    SelectedLines,
  },

  data() {
    return {
      starttime: "",
      endtime: "",
      starttimeprops:"",
      endtimeprops:"",
      lines_option: {
        title: {
          text: 'all'
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
        ]
      },
    };
  },
  // mounted() {
  //   this.$refs.chart1.drawChart();
  //   this.$refs.chart2.drawChart();
  // },

  methods: {

    confirm(){
      this.starttimeprops = this.starttime;
      this.endtimeprops = this.endtime;
    },

    pgmajfault_update(data){
      // this.lines_option.xAxis.data = data.xAxis.data;
      this.$set(this.lines_option.series,1, data.series[0]);
      // this.lines_option.series[0] = data.series[0];

    },

    pgfault_update(data) {
      this.lines_option.xAxis.data = data.xAxis.data;
      console.log("data emit", data)
      // this.lines_option.series[0] = data.series[0];
      this.$set(this.lines_option.series,0, data.series[0]);
      console.log(this.lines_option);
    },

    disk_read_update(data){
      this.$set(this.lines_option.series,2, data.series[0]);
      this.$set(this.lines_option.series,3, data.series[1]);
    },

    disk_written_update(data){
      this.$set(this.lines_option.series,4, data.series[0]);
      this.$set(this.lines_option.series,5, data.series[1]);
    },

    mem_update(data){
      this.$set(this.lines_option.series,6, data.series[0]);
    },

    disk_io_update(data){
      this.$set(this.lines_option.series,7, data.series[0]);
      this.$set(this.lines_option.series,8, data.series[1]);
    },

    intr_update(data){
      this.$set(this.lines_option.series,9, data.series[0]);
    },

    cs_update(data){
      this.$set(this.lines_option.series,10, data.series[0]);
    },

    cpu_update(data){
      this.$set(this.lines_option.series,11, data.series[0]);
      this.$set(this.lines_option.series,12, data.series[1]);
      this.$set(this.lines_option.series,13, data.series[2]);
      this.$set(this.lines_option.series,14, data.series[3]);
      this.$set(this.lines_option.series,15, data.series[4]);
    },

  },
};
</script>



<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
