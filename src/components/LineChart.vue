<template>
  <div>
    <apexchart
      width="500"
      type="line"
      :options="chartOptions"
      :series="this.graphData"
    ></apexchart>
    <!-- 
      :series="series" doesn't update the graph when you select a stock. 
      I don't know why because it should be linked to this.graphData which changes. 
      To circumvent that, I sidestepped any usage of the series variable. It seems to work fine especially
      since this.graphData includes all graphs/stocks by default.
 -->
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

      series: this.graphData,
    };
  },
  methods: {},
  components: {
    apexchart: VueApexCharts,
  },
  props: {
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