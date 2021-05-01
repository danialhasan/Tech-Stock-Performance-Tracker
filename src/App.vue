<template>
  <SearchBar />
  <Graph />
  <StockList />
  <Navbar />

  <!-- TODO: List component. -->
</template>

<script>
import SearchBar from "./components/SearchBar";
import Graph from "./components/Graph";
import Navbar from "./components/Navbar";
import StockList from "./components/StockList";

export default {
  name: "App",
  components: { SearchBar, Graph, Navbar, StockList },
  data() {
    return {
      stocks: [],
    };
  },
  beforeMount() {
    /**
     * Before mounting, get the info for all stocks in this.stocks
     * from the API.
     */
  },
  methods: {
    getInfo(stock) {
      console.log(stock);
      return;
    },
  },
};
</script>
