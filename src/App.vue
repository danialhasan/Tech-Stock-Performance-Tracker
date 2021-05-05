<template>
  <SearchBar />
  <Graph />
  <StockList :Stocks="this.stocks" @remove-stock="removeStock" />

  <Navbar />
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
      stockNameInfo: [
        {
          //quotes are inconsistent but it makes no functional difference. Reason? ESlint.
          Symbol: "A",
          Name: "Agilent Technologies Inc. Common Stock",
          "Last Sale": "$133.64",
          "Net Change": -0.51,
          "% Change": "-0.38%",
          "Market Cap": "40719775905.00",
          Country: "United States",
          "IPO Year": 1999,
          Volume: 1514161,
          Sector: "Capital Goods",
          Industry: "Electrical Products",
        },
        {
          Symbol: "AA",
          Name: "Alcoa Corporation Common Stock ",
          "Last Sale": "$36.64",
          "Net Change": -0.53,
          "% Change": "-1.426%",
          "Market Cap": "6830027702.00",
          Country: "",
          "IPO Year": 2016,
          Volume: 6280970,
          Sector: "Basic Industries",
          Industry: "Metal Fabrications",
        },
        {
          Symbol: "AAC",
          Name: "Ares Acquisition Corporation Class A Ordinary Shares",
          "Last Sale": "$9.83",
          "Net Change": 0.01,
          "% Change": "0.102%",
          "Market Cap": "1228872875.00",
          Country: "",
          "IPO Year": 2021,
          Volume: 901485,
          Sector: "Finance",
          Industry: "Business Services",
        },
      ],
    };
  },
  beforeMount() {
    //Get some data to populate our data stores
    let stock = ["AAPL", "FB", "GNL", "TSLA", "NFLX"];

    //comment this out to avoid being rate limited
    // stock.forEach((stockTicker) => {
    //   this.getInfoAPI(stockTicker);
    // });
    this.trimDatabase();
  },
  methods: {
    removeStock(symbol) {
      delete this.stocks[symbol];
    },
    trimDatabase() {
      /**
       * Iterate through array.
       * For each index, iterate through object keys.
       * If key does not match "name" or "symbol", delete key.
       */
      for (var i = 0; i < this.stockNameInfo.length; i++) {
        for (let key in this.stockNameInfo[i]) {
          if (key != "Name" && key != "Symbol") {
            delete this.stockNameInfo[i][key];
          }
        }
      }
      console.log(this.stockNameInfo);
    },
    async getInfoAPI(stock) {
      // Recently I've been running low on requests since I'm only allowed 1000.
      // It is too small. I will make several accounts, since I have around 6 emails.
      // Excluding this account, I will have 5000 requests to use every month if I recycle
      // the API keys.
      const endpoint = "http://api.marketstack.com/v1/eod";
      const apiKey = "71aea0f9ae7c2fcb0d5ddff108131d24"; // for email 1
      // const apiKey = "4001098532f1de7d5e26baff968871d7"; // for email 2

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
        })
        .catch((err) => {
          console.log(err);
          if (err.response.status == 422) {
            console.log(
              "Inputted ticker does not exist. Input a real ticker and try again."
            );
          }
          // delete stock from stocks data object, because
          // a failed get request means the stock inputted is not a real stock.
          // we only want real stock tickers in our this.stocks data object.
          delete this.stocks.stock;
        });
    },
  },
};
</script>
