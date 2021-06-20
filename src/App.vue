<!-- eslint-disable max-len -->
<template>
  <div class="container mx-auto flex flex-col items-center bg-gray-100 p-4">
    <div class="container">
      <VTickerForm @formSubmit="addTicker" />
      <hr class="w-full border-t border-gray-600 my-2">
      <dl class="mt-4 grid grid-cols-1 gap-5 sm:grid-cols-3">
        <VTickerCard
          v-for="ticker in tickers"
          :key="ticker.name"
          :ticker="ticker"
          :is-selected="selectedTicker === ticker"
          @click="selectedTicker = ticker"
          @onDelete="onDelete($event)"
        />
      </dl>
      <hr class="w-full border-t border-gray-600 my-4">
      <VTickerChart />
    </div>
  </div>
</template>

<script>
import VTickerForm from './components/VTickerForm.vue';
import VTickerCard from './components/VTickerCard.vue';
import VTickerChart from './components/VTickerChart.vue';
import { key } from './key';

export default {
  name: 'App',
  components: {
    VTickerForm,
    VTickerCard,
    VTickerChart,
  },
  data() {
    return {
      tickers: [
        {
          name: 'BTC',
          price: 44000,
        },
      ],
      selectedTicker: null,
      updateInterval: undefined,
    };
  },
  computed: {
    updatedTicker: {
      // eslint-disable-next-line max-len
      get() { return this.tickers.findIndex((ticker) => ticker.name === this.selectedTicker.name); },
      set(value) { this.tickers[this.updatedTicker].price = value; },
    },
  },
  watch: {
    selectedTicker: {
      handler(selectedTicker, oldSelectedTicker) {
        if (selectedTicker.name && !oldSelectedTicker) {
          this.updateInterval = setInterval(async () => {
            this.updatedTicker = await this.fetchCryptoCurrency(selectedTicker.name);
          }, 1000);
        } else {
          clearInterval(this.updateInterval);
          this.updateInterval = setInterval(async () => {
            this.updatedTicker = await this.fetchCryptoCurrency(selectedTicker.name);
          }, 1000);
        }
      },
    },
  },
  methods: {
    async addTicker(tickerName) {
      const isExist = this.tickers.find((ticker) => ticker.name === tickerName);

      if (!isExist) {
        const ticker = { name: tickerName, price: 0 };
        ticker.price = await this.fetchCryptoCurrency(ticker.name);
        this.tickers.push(ticker);
      }
    },
    async fetchCryptoCurrency(tickerName) {
      const response = await fetch(`https://min-api.cryptocompare.com/data/price?fsym=${tickerName}&tsyms=USD&api_key=${key}`);
      const data = await response.json();
      return data.USD;
    },

    onDelete(tickerName) {
      this.tickers = this.tickers.filter((ticker) => ticker.name !== tickerName);
      clearInterval(this.updateInterval);
    },
  },
};
</script>

<style>
.container {
  height: 100%;
}
</style>
