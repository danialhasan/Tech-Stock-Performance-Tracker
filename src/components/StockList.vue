<template>
  <div class="bg-green-500 w-96 flex table-fixed h-44 overflow-x-scroll">
    <table>
      <thead>
        <tr class="w-1/2">
          <th>Header</th>
          <th>Header</th>
          <th>Header</th>
          <th>Header</th>
          <th>Header</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
        </tr>
        <tr>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
        </tr>
        <tr>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
        </tr>
        <tr>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
        </tr>
        <tr>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
        </tr>
        <tr>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
          <td>Data</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
<script>
export default {
  name: "StockList",
  data() {
    return {
      stocks: this.Stocks,
    };
  },
  mounted() {
    console.log(this.stocks);
  },
  props: ["Stocks"],
};
</script>