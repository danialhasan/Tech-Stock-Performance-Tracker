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
      stockList: ["AAPL", "FB"],
      // "AAPL", "FB", "GNL", "TSLA"
      testData: [
        {
          name: "series-1",
          data: [30, 40, 35, 50, 49, 60, 70, 91],
        },
      ],
      datasets: [],
      apiCalled: false,
    };
  },
  created() {
    //lifecycle hooks are always synchronous, never async.
    //Get some data to populate our data stores
    // let stock = ["AAPL", "FB", "GNL", "TSLA"];
    let stock = this.stockList;
    let dataRecieved = false;
    // comment this out to avoid being rate limited
    stock.forEach((stockTicker, i) => {
      this.getInfoAPI(stockTicker);
      // var tradingDays = this.stocks[stockTicker].lastWeekTradingDays;

      //tradingDays is equal to an empty object because it's accessed before this.getInfoAPI finishes the
      //api request, that would fill up the variable with data. So, we need to form the datasets array
      //after the API request is fulfilled.

      // console.log(tradingDays);
      // console.log(i);
      //
      // if (i == stock.length - 1) {
      //   dataRecieved = true;
      // }
      this.apiCalled = true;
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
      /*
      For each stock in the list of stocks, create one array.
      For each indice in the stock's lastWeekTradingDays array, 
      push the close key.
      */
      stock.forEach((stockTicker, i) => {
        let priceArray = [];
        // placed in the loop so it resets every time the loop goes to another stock.
        // This is done so that a new priceArray of fresh values is pushed to the dataset.

        // this.stocks[stockTicker].lastWeekTradingDays is of type OBJECT, not array.
        for (var day in this.stocks[stockTicker].lastWeekTradingDays) {
          priceArray.push(
            this.stocks[stockTicker].lastWeekTradingDays[day].close
          );
          //accessing the price data of the stock for each day of last week
        }
        this.datasets.push(priceArray);
      });
      console.log(this.datasets);
      /*
      The first time this.datasets is logged, it carries the data of the first stock and not
      the second stock because the api call for the first one has been completed, whereas the api
      call for the second one has not.

      There are two ways to fix this:
      1. Find some way to call this.createDatasets once, after all the API calls have been made.
      2. Let this.createDatasets be called multiple times in between API calls with incomplete data. 
         Then, parse it for discrepencies (where an array index is null or two are duplicates) and 
         deal with it promptly.

      In the end, we're passing the dataset to the lineChart component through props. We do not want to 
      chart malformed data.
      
      */
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
      /**
       * TODO
       * Add validation. If stock API call fails, don't add it to the stock list
       */

      const endpoint = "http://api.marketstack.com/v1/eod";
      // const apiKey = "71aea0f9ae7c2fcb0d5ddff108131d24"; // for email 1, rate limit reached for month of May
      const apiKey = "4001098532f1de7d5e26baff968871d7"; // for email 2

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

          /**To avoid the problem of not being able to await this function in
           * a lifecycle hook for some reason, I'll try to form the datasets here.
           */
          // MAKE SURE THIS EXECUTES LAST, AFTER ALL THE PREVIOUS ASYNC CODE.
          // Note, since this is in the async api function, it'll get called however many stocks there are.
          // For example, if there are 4 stocks in stockList, it'll get called 4 times. This might make duplicates.
          this.createDatasets(this.stockList);
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
