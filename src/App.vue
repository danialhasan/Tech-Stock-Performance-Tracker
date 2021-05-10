<template>
  <SearchBar @stock_submission="this.getInfoAPI" />
  <!-- <Graph /> -->
  <StockList
    :Stocks="this.stocks"
    @remove-stock="removeStock"
    @stock-selected="stockSelected"
  />
  <line-chart :graphData="this.graphDatasets" :tradingDays="this.tradingDays" />
  <!-- I changed the graphData component from this.datasets to this.graphDatasets, because the value of the prop
  will change depending on which stock is clicked. Since I don't want to mutate this.datasets, I changed it to a 
  variable that can point to this.datasets or become its own thing. this.graphDatasets points to this.datasets
  by default, which is its default configuration. When no stocks are selected, it will automatically point to that. 
  In other cases, it will point to individual datasets within datasets. ~-->
  <!-- <Navbar /> -->
  <!-- hidden until we can use it -->
</template>

<script>
import SearchBar from "./components/SearchBar";
// import Navbar from "./components/Navbar";
import StockList from "./components/StockList";
import LineChart from "./components/LineChart";
import axios from "axios";
import intlFormat from "date-fns/intlFormat";

export default {
  name: "App",
  components: { SearchBar, /*Navbar,*/ StockList, LineChart },
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
      graphDatasets: [],
      tradingDays: [],
    };
  },
  created() {
    // get api info for all starter stocks
    let stock = this.stockList;
    stock.forEach((stockTicker, i) => {
      this.getInfoAPI(stockTicker);
    });
    // get last 7 trading days for graph
    this.getLastWeekTradingDays(new Date());
    //set graphDatasets to datasets as default
    this.graphDatasets = this.datasets;
  },
  methods: {
    stockSelected(stockName) {
      this.graphDatasets = [];
      console.log(stockName);
      // We want to take the stockName and get it's dataset. Then we want to
      // pass that individual dataset into the graph component through props.
      // Just get the stockName dataset through Dataset. It's already there when
      // createDatasets is called with all stocks.
      console.log(this.datasets);
      this.datasets.forEach((stock) => {
        if (stock.name == stockName) {
          let data = {
            name: stockName,
            data: stock.data,
          };
          this.graphDatasets.push(data);
          console.log(this.graphDatasets);
        }
      });
    },
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
      let graphData = {
        name: stock,
        data: [],
      };
      for (var day in this.stocks[stock].lastWeekTradingDays) {
        graphData.data.push(this.stocks[stock].lastWeekTradingDays[day].close);
        //accessing the price data of the stock for each day of last week
      }
      this.datasets.push(graphData);
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
       * - Add validation. If stock API call fails, don't add it to the stock list
       * - Add duplicate check. If stock is already in stockList, don't add.
       */

      const endpoint = "http://api.marketstack.com/v1/eod";
      const apiKey = "ce8cc7f3923c691bfd0b1aa10aa5b594";

      if (!(stock in this.stocks)) {
        this.stocks[stock] = {
          today: {},
          lastThreeTradingDays: {},
          lastWeekTradingDays: {},
        };
      } else {
        console.log(`Duplicate: ${stock}`);
        return;
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
