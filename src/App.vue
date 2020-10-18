<template>
  <div id="app">
    <section class="favourite-currency-wrapper">
      <h3>Favourites currency</h3>
      <div class="favourite-currency-container">
        <div class="favourite-currency" v-for="favCurrency in apiRequests" :key="favCurrency.code" v-on:click="chooseCurrency">
          {{favCurrency.code}}
          <input type="checkbox"
          v-model="selectedCurrency"
          v-bind:value="favCurrency.code">
        </div>
      </div>  
    </section>

    <section v-if="errored">
      <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
    </section>

    <section v-else>
      <div v-if="loading">Loading...</div>
      <table v-else class="currency-table">
        <thead>
          <th>Name</th>
          <th>Code</th>
          <th>Exchange</th>
        </thead>
        <tbody>
          <tr
            v-for="currency in selectedItems"
            :key="currency.code"
          >
            <td>{{currency.currency}}</td>
            <td>{{ currency.code }}</td>
            <td>{{ currency.mid | currencydecimal }}zł</td>
          </tr>
        </tbody>
      </table>
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
      selectedItems: function(){
        if(this.selectedCurrency.length != 0) {
          return this.apiRequests.filter(function (item) {
            return this.selectedCurrency.includes(item.code);
          }, this);
        }else{
          return this.apiRequests;
        }
      }
    },
  methods: {
    chooseCurrency(e) {
      if(e.target.classList.contains("choosen")){
        if(confirm('Are you sure you want to remove currency from favourite list?')){
          e.target.querySelector('input').click();
          e.target.classList.remove("choosen");
        }
      }else{
        e.target.querySelector('input').click();
        e.target.classList.add("choosen");
      }
    }
  },
  mounted() {
    axios
      .get('https://api.nbp.pl/api/exchangerates/tables/A/')
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
*,*::after,*::before{
  box-sizing: border-box;
}
body{
  font-family: 'Montserrat', sans-serif;
}
#app{
  display: flex;
  justify-content: center;
  width:100%;
}
.currency-table{
  width:500px;
  text-align: center;
  overflow: scroll;
}
.currency-table tbody td{
  width:33%;
  padding: 7.5px 0px;
}
.currency-table tbody tr:nth-child(even) {background: #CCC}
.currency-table tbody tr:nth-child(odd) {background: #FFF}

.favourite-currency{
  cursor:pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 45px;
}
.favourite-currency:hover{
  background: rgba(136, 255, 0, 0.356);
}
.favourite-currency input{
  display:none;
}
.favourite-currency-container{
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.favourite-currency-wrapper{
  position: fixed;
  right: 15px;
}
.choosen{
  background: chartreuse;
  border: green 1px solid;
}
.choosen:hover{
  background:rgba(255, 51, 0, 0.582);
}
</style>
