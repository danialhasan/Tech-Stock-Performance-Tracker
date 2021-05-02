<template>
  <SearchBar />
  <Graph />
  <StockList :Stocks="this.stocks" />
  <Navbar />

  <!-- TODO: List component. -->
</template>

<script>
import SearchBar from "./components/SearchBar";
import Graph from "./components/Graph";
import Navbar from "./components/Navbar";
import StockList from "./components/StockList";
import axios from "axios";

export default {
  name: "App",
  components: { SearchBar, Graph, Navbar, StockList },
  data() {
    return {
      stocks: {},
    };
  },
  beforeMount() {
    //Get some data to populate our data stores
    this.getInfo("AAPL");
    this.getInfo("TSLA");
    this.getInfo("QQQ");
  },
  methods: {
    async getInfo(stock) {
      const endpoint = "http://api.marketstack.com/v1/eod";
      const apiKey = "71aea0f9ae7c2fcb0d5ddff108131d24";

      // If stock is not found in stocks object, add it.
      if (!(stock in this.stocks)) {
        this.stocks[stock] = {
          today: {},
          lastThreeTradingDays: {},
          lastWeekTradingDays: {},
        };
      }

      axios
        .get(`${endpoint}?access_key=${apiKey}&symbols=${stock}`)
        .then((response) => {
          // let handling data be more readable
          let stockData = response.data.data;
          // access our stock object in this.stocks so we can add data to it
          let stockInfo = this.stocks[stock];

          let latestTradingDay = stockData[0];
          let lastThreeTradingDays = [stockData[0], stockData[1], stockData[2]];
          let lastWeekTradingDays = [
            stockData[0],
            stockData[1],
            stockData[2],
            stockData[3],
            stockData[4],
            stockData[5],
            stockData[6],
          ];

          stockInfo.today = latestTradingDay;
          stockInfo.lastThreeTradingDays = lastThreeTradingDays;
          stockInfo.lastWeekTradingDays = lastWeekTradingDays;
        })
        .catch((err) => {
          console.log(err);
          if (err.response.status == 422) {
            console.log(
              "Inputted ticker does not exist. Input a real ticker and try again."
            );
          }
          //delete stock from stocks data object, because
          // a failed get request means the stock inputted is not a real stock.
          //we only want real stock tickers in our this.stocks data object.
          delete this.stocks.stock;
        });
    },
  },
};
</script>
