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
    };
  },
  computed: {
  },
  methods: {
    addTicker(tickername) {
      const ticker = { name: tickername, price: 0 };
      this.tickers.push(ticker);
    },
  },
};
</script>

<style>
.container {
  height: 100%;
}
</style>
