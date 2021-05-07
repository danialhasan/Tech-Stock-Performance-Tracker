<template>
  <canvas id="stock_graph" class=""> </canvas>
</template>
<script>
import { Line } from "vue-chartjs";

export default {
  name: "Graph",
  data() {
    return {};
  },
  mounted() {
    const ctx = document.getElementById("stock_graph");
    new Line(ctx, this.planetChartData);
  },
};
</script>