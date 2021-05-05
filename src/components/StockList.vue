<template>
  <!-- TODO: Display passed down data from props.  -->
  <div class="mt-12 mb-36 flex items-start px-2">
    <div class="overflow-x-auto w-full">
      <table
        class="mx-auto max-w-4xl w-full whitespace-nowrap rounded-lg bg-white divide-y divide-gray-300 overflow-hidden"
      >
        <thead class="bg-gray-50">
          <tr class="text-gray-600 text-left">
            <th class="font-semibold text-sm uppercase px-6 py-4">Ticker</th>
            <th class="font-semibold text-sm uppercase px-6 py-4">Open</th>
            <th class="font-semibold text-sm uppercase px-6 py-4 text-center">
              Close
            </th>
            <th class="font-semibold text-sm uppercase px-6 py-4 text-center">
              High
            </th>
            <th class="font-semibold text-sm uppercase px-6 py-4">Low</th>
            <th class="font-semibold text-sm uppercase px-6 py-4"></th>
          </tr>
        </thead>
        <tbody class="divide-y divide-gray-200">
          <tr v-for="stock in this.stocks" :key="stock">
            <td class="pl-6 pr-12 py-4">
              <div class="flex items-center space-x-3">
                <p class="text-xl">${{ stock.today.symbol }}</p>
              </div>
            </td>
            <td class="px-6 py-4">
              <p class="text-xl">${{ stock.today.open }}</p>
            </td>
            <!-- If close > open, bg-green-200. Else, bg-red-200. -->
            <td class="px-6 py-4 text-center">
              <span
                class="font-semibold px-2 rounded-full text-xl"
                :class="[
                  stock.today.close > stock.today.open
                    ? 'bg-green-200 text-green-800'
                    : 'bg-red-200 text-red-800',
                ]"
              >
                ${{ stock.today.close }}
              </span>
            </td>
            <td class="px-6 py-4 text-center">
              <p class="text-xl">${{ stock.today.high }}</p>
            </td>
            <td class="px-6 py-4 text-center">
              <p class="text-xl">${{ stock.today.low }}</p>
            </td>
            <td
              @click="removeStock(stock.today.symbol)"
              class="px-6 py-4 text-center"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                class="h-6 w-6"
                fill="none"
                viewBox="0 0 24 24"
                stroke="#EB5757"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M6 18L18 6M6 6l12 12"
                />
              </svg>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
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
  methods: {
    removeStock(symbol) {
      this.$emit("remove-stock", symbol);
    },
  },
  props: ["Stocks"],
  emits: ["remove-stock"],
};
</script>