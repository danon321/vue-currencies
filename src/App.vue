<template>
  <div id="app">
    <section v-if="errored">
      <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
    </section>

    <section v-else>
      <div v-if="loading">Loading...</div>

      <div
        v-else
        v-for="currency in info"
        :key="currency.code"
      >
        {{currency.currency}}
        ({{ currency.code }}): 
        {{ currency.mid | currencydecimal }}zł
      </div>
    </section>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      info: null,
      loading: true,
      errored: false
    }
  },
  filters: {
  currencydecimal (value) {
    return value.toFixed(2)
    }
  },

  mounted() {
    axios
      .get('http://api.nbp.pl/api/exchangerates/tables/A/today/')
      .then(response => {
        this.info = response.data[0].rates
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
  }
}
</script>

<style>
</style>
