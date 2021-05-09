<template>
  <div>
    <apexchart
      width="500"
      type="line"
      :options="chartOptions"
      :series="series"
    ></apexchart>
  </div>
</template>

<script>
import VueApexCharts from "vue3-apexcharts";

export default {
  data() {
    return {
      lastWeekTradingDays: [],
      chartOptions: {
        chart: {
          id: "vuechart-example",
        },
        xaxis: {
          // categories: [1991, 1992, 1993, 1994, 1995, 1996, 1997, 1998],
          categories: this.tradingDays,
          //Must set these to the last 7 trading days relative to today
        },
      },
      // series: [
      //   {
      //     name: "series-1",
      //     data: [30, 40, 35, 50, 49, 60, 70, 91],
      //   },
      //   {
      //     name: "series-2",
      //     data: [60, 30, 15, 70, 39, 10, 0, 100],
      //   }
      // ],
      series: this.graphData,
    };
  },
  methods: {},
  components: {
    apexchart: VueApexCharts,
  },
  props: {
    // graphOptions: {
    //   type: Object,
    // },
    graphData: {
      type: Array,
      default: () => [1, 2, 3, 4],
    },
    graphId: {
      type: String,
      default: "Graph Title Here",
    },
    tradingDays: {
      type: Array,
      default: () => [1, 2, 3, 4],
    },
  },
};
</script>