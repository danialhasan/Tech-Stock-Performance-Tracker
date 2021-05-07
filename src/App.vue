<template>
  <SearchBar @stock_submission="this.getInfoAPI" />
  <!-- <Graph /> -->
  <StockList :Stocks="this.stocks" @remove-stock="removeStock" />
  <line-chart :graphData="this.testData" />
  <Navbar />
</template>

<script>
import SearchBar from "./components/SearchBar";
import Navbar from "./components/Navbar";
import StockList from "./components/StockList";
import LineChart from "./components/LineChart";
import axios from "axios";
// import LineChart from "./modules/LineChart.vue";

export default {
  name: "App",
  components: { SearchBar, Navbar, StockList, LineChart },
  data() {
    return {
      stocks: {},
      stockList: ["AAPL", "FB", "GNL"],
      // "AAPL", "FB", "GNL", "TSLA"
      testData: [
        {
          name: "series-1",
          data: [30, 40, 35, 50, 49, 60, 70, 91],
        },
      ],
      datasets: [],
    };
  },
  created() {
    //lifecycle hooks are always synchronous, never async.
    //Get some data to populate our data stores
    // let stock = ["AAPL", "FB", "GNL", "TSLA"];
    let stock = this.stockList;
    stock.forEach((stockTicker, i) => {
      this.getInfoAPI(stockTicker);
    });
    // console.log(dataRecieved);
    // if (dataRecieved) {
    //   this.createDatasets(stock);
    //   console.log("All Stock API data recieved! Stock data:");
    //   console.log(Object.values(this.stocks["AAPL"]));
    // }
    /**
     * Create datasets array. This array houses dataset arrays, each of which represents the
     * dataset used to graph the price fluctuations of each stock. Since it's an array of arrays,
     * you can't access the dataset arrays of specific companies without knowing their indice value
     * in the 'stock' array of stock tickers.
     *
     * DATA STRUCTURE
     * datasets = [ // for 3 stocks
     *  [  //"this.stocks[STOCK].lastWeekTradingDays", for AAPL...
     *   "this.stocks[stock].lastWeekTradingDays.0.close"
     *   "this.stocks[stock].lastWeekTradingDays.1.close"
     *   "this.stocks[stock].lastWeekTradingDays.2.close"
     *   "this.stocks[stock].lastWeekTradingDays.3.close"
     *   "this.stocks[stock].lastWeekTradingDays.4.close"
     *   "this.stocks[stock].lastWeekTradingDays.5.close"
     *   "this.stocks[stock].lastWeekTradingDays.6.close"
     * ],
     *  [...], //for TSLA...
     *  [...], //for GNL...
     * ]
     */
  },
  mounted() {
    // this.createDatasets(this.stockList);
  },
  methods: {
    createDatasets(stock) {
      //individual stock ticker
      let priceArray = [];
      for (var day in this.stocks[stock].lastWeekTradingDays) {
        priceArray.push(this.stocks[stock].lastWeekTradingDays[day].close);
        //accessing the price data of the stock for each day of last week
      }
      this.datasets.push(priceArray);
      //Sometimes this.datasets is reverse in relation to this.stocks. It only happens sometimes
      //I'm not sure if it's even happening in the first place, since vue devtools is not stable with
      //Vue 3.
    },
    removeStock(symbol) {
      delete this.stocks[symbol];
    },
    trimDatabase() {
      for (var i = 0; i < this.stockNameInfo.length; i++) {
        for (let key in this.stockNameInfo[i]) {
          if (key != "Name" && key != "Symbol") {
            delete this.stockNameInfo[i][key];
          }
        }
      }
    },
    async getInfoAPI(stock) {
      //individual stock ticker
      /**
       * TODO
       * Add validation. If stock API call fails, don't add it to the stock list
       */

      const endpoint = "http://api.marketstack.com/v1/eod";
      // const apiKey = "71aea0f9ae7c2fcb0d5ddff108131d24"; // for email 1, rate limit reached for month of May
      const apiKey = "4001098532f1de7d5e26baff968871d7"; // for email 2

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
          let stockData = response.data.data;
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
          this.createDatasets(stock);
        })
        .catch((err) => {
          console.log(err);
          if (err.response.status == 422) {
            console.log(
              "Inputted ticker does not exist. Input a real ticker and try again."
            );
          }

          delete this.stocks.stock;
        });
    },
  },
};
</script>
