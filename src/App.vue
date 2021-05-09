<template>
  <SearchBar @stock_submission="this.getInfoAPI" />
  <!-- <Graph /> -->
  <StockList :Stocks="this.stocks" @remove-stock="removeStock" />
  <line-chart :graphData="this.datasets" :tradingDays="this.tradingDays" />
  <Navbar />
</template>

<script>
import SearchBar from "./components/SearchBar";
import Navbar from "./components/Navbar";
import StockList from "./components/StockList";
import LineChart from "./components/LineChart";
import axios from "axios";
import intlFormat from "date-fns/intlFormat";

export default {
  name: "App",
  components: { SearchBar, Navbar, StockList, LineChart },
  data() {
    return {
      stocks: {},
      stockList: ["AAPL", "FB", "GNL", "TSLA"],
      // "AAPL", "FB", "GNL", "TSLA"
      testData: [
        {
          name: "series-1",
          data: [30, 40, 35, 50, 49, 60, 70, 91],
        },
      ],
      datasets: [],
      tradingDays: [],
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
    console.log(this.datasets);
    this.getLastWeekTradingDays(new Date());
    // this.getLastWeekTradingDays(new Date('Tue May 23 2021 02:43:53 GMT-0400 (Eastern Daylight Time)'));
    // Change the date in the above line in new Date() to simulate
    // different start dates.
  },
  methods: {
    getLastWeekTradingDays(today) {
      /** NOTE
       * This function hasn't been extensively tested yet.
       */
      this.tradingDays = [];

      let currentDate = today;
      let endDate = new Date(currentDate);
      endDate.setDate(endDate.getDate() - 10);

      while (endDate <= currentDate) {
        //<= for current day on end of array, < for not.
        // if endDate is a weekend, skip it.
        if (endDate.getDay() == 0 || endDate.getDay() == 6) {
          console.log("Weekend!");
          endDate.setDate(endDate.getDate() + 1);
        } else {
          this.tradingDays.push(new Date(endDate));
          endDate.setDate(endDate.getDate() + 1);
        }
      }

      for (let i = 0; i < this.tradingDays.length; i++) {
        this.tradingDays[i] = intlFormat(this.tradingDays[i]);
      }
    },
    createDatasets(stock) {
      //individual stock ticker
      let graphData = {
        name: stock,
        data: [],
      };
      for (var day in this.stocks[stock].lastWeekTradingDays) {
        graphData.data.push(this.stocks[stock].lastWeekTradingDays[day].close);
        //accessing the price data of the stock for each day of last week
      }
      /**
       *  I want to change the dataSets array to not be an array of arrays, but rather an array of
       * objects. I want to emulate the structure of this.testData. So, I'll push
       * an object instead of priceArray to this.datasets.
       */
      this.datasets.push(graphData);
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
      const apiKey = "ce8cc7f3923c691bfd0b1aa10aa5b594";

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
