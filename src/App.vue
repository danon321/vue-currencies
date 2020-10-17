<template>
  <div id="app">
    <div class="favourite-currency-wrapper">
      <div class="favourite-currency" v-for="favCurrency in apiRequests" :key="favCurrency.code">
        <label>{{favCurrency.code}}</label>
        <input type="checkbox"
        v-model="selectedCurrency"
        v-bind:value="favCurrency.code">
      </div>
    </div>
    <section v-if="errored">
      <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
    </section>

    <section v-else>
      <div v-if="loading">Loading...</div>

      <div
        v-else
        v-for="currency in selectedItems"
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
      loading: true,
      errored: false,
      apiRequests:[],
      selectedCurrency: []
    }
  },
  filters: {
  currencydecimal (value) {
    return value.toFixed(3)
    }
  },
  computed: {
      selectedItems: function() {
        if(this.selectedCurrency.length != 0) {
          return this.apiRequests.filter(function (item) {
            return this.selectedCurrency.includes(item.code);
          }, this);
        }else{
          return this.apiRequests;
        }
      },
    },
  mounted() {
    axios
      .get('http://api.nbp.pl/api/exchangerates/tables/A/')
      .then(response => {
        this.apiRequests = response.data[0].rates
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
#app{
  width:100%;
  text-align: center;
}
.favourite-currency-wrapper{
  position: fixed;
  right: 0px;
}
</style>
