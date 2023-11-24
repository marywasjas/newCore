<template>
  <div :class="className" :style="{ height: height, width: width }" />
</template>

<script>
import * as echarts from "echarts";
import resize from "../mixins/resize";

export default {
  name: "BarChart",
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: "chart",
    },
    width: {
      type: String,
      default: "100%",
    },
    height: {
      type: String,
      default: "100%",
    },
    chartData: {
      type: Array,
      default: () => {
        return [];
      },
    },
    title: {
      type: String,
      default: "",
    },
    legend: {
      type: String,
      default: "",
    },
  },

  data() {
    return {
      chart: null,
    };
  },

  watch: {
    chartData: {
      handler(newVal, oldVal) {
        this.initChart();
      },
      deep: true,
    },
  },

  mounted() {
    this.$nextTick(() => {
      this.initChart();
    });
  },

  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    this.chart.dispose();
    this.chart = null;
  },

  methods: {
    initChart() {
      this.chart = echarts.init(this.$el);
      let yData = this.chartData.yData.map((el) => {
        let obj = {
          ...el,
          type: "bar",
          stack: "total",
          emphasis: {
            focus: "series",
          },
        };
        return obj;
      });
      const option = {
        title: {
          text: this.title ? this.title : "",
          textStyle: {
            fontWeight: "normal",
            fontSize: "12",
          },
          left: 40,
          top: 5,
        },
        tooltip: {
          trigger: "axis",
          axisPointer: {
            type: "shadow", // 'shadow' as default; can also be 'line' or 'shadow'
          },
        },
        legend: {
          top: 25,
          right: 100,
        },
        xAxis: {
          type: "category",
        },
        yAxis: {
          type: "value",
          data: this.chartData.xData,
        },
        grid: {
          // left: 15,
          // right: 10,
          // bottom: "15%",
          // top: "5%",
          // containLabel: true, // 保持直角坐标系内绘图网格
        },
        series: yData,
      };

      this.chart.setOption(option);
    },
  },
};
</script>
