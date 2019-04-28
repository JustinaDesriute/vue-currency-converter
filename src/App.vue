<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span class="font-weight-light">currency converter</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <span>The base currency: {{ base }} on {{ date }}</span>
      <v-spacer></v-spacer>
      <v-tooltip bottom>
          <v-btn
            flat
            href="https://www.linkedin.com/in/justina-de%C5%A1ri%C5%ABt%C4%97-966153101/"
            target="_blank"
            slot="activator"
          >
            <span>built by: Justina Desriute</span>
          </v-btn>
        go to LinkedIn profile
      </v-tooltip>
    </v-toolbar>

    <v-content>
      <div class="currency-lists-container">
        <AmountInputField class="currency-list-item" v-on:amountSet="setEnteredAmount" />
        <AutocompleteInput id="convertFrom" class="currency-list-item" v-on:currencySelected="setCurrencyRate" currency="Convert From" :countriesList="countries"/>
        <AutocompleteInput id="convertTo" class="currency-list-item" v-on:currencySelected="setCurrencyRate" currency="Convert To" :countriesList="countries"/>
        <v-btn class="currency-list-item" @click="convertCurrency">convert</v-btn>
        <v-text-field class="currency-list-item" 
            placeholder="converted amount"
            :value="convertedResult"
            outline
            readonly
        ></v-text-field>
      </div>
      <DatePicker/>
      <ErrorHandler/>
    </v-content>

    <v-footer class="pa-3">
      <v-spacer></v-spacer>
      <div>&copy; {{ new Date().getFullYear() }} contact me on LINKEDIN or check out my GitHub profile</div>
    </v-footer>
  </v-app>
</template>

<style src="./components/layout-styles.css"></style>

<script>
import AutocompleteInput from './components/AutocompleteInput'
import DatePicker from './components/DatePicker'
import ErrorHandler from './components/ErrorHandler'
import AmountInputField from './components/AmountInputField'

import axios from 'axios';

export default {
  name: 'App',
  components: {
    AutocompleteInput,
    DatePicker,
    ErrorHandler,
    AmountInputField,
  },
  data () {
    return {
      base: String,
      date: String,
      convertedResult: Number,
      countries: Array,
    }
  },
  props: {
    currency: String,
    enteredAmount: Number,
    currencyRate: Number,
    convertFromCurrencyRate: Number,
    convertToCurrencyRate: Number,
  },

  methods: {
    setEnteredAmount(value) {
      this.enteredAmount = value;
      console.log('enteredAmount is:', this.enteredAmount);
    },

    setCurrencyRate(value, id) {

      const currencyRate = Object.keys(this.countryRatePair)
        .filter(key => key == value)    
        .reduce((obj, key) => {
          obj[key] = this.countryRatePair[key];
          return parseFloat(Object.values(obj));
        }, {});

      if (id == 'convertFrom') {
        this.convertFromCurrencyRate = currencyRate;
      } else {
        this.convertToCurrencyRate = currencyRate;
      }
    },

    convertCurrency() {
      console.log('CONVERTING', this.enteredAmount, this.convertFromCurrencyRate, this.convertToCurrencyRate);  
      this.convertedResult = (parseInt(this.enteredAmount) * parseInt(this.convertFromCurrencyRate)) * parseInt(this.convertToCurrencyRate);
    }
  },

  mounted() {
      axios
      // check how to add the current date as a url parameter
      .get('https://api.exchangeratesapi.io/2019-04-27')
      .then(response => {
        this.base = response.data.base;
        this.date = response.data.date;
        this.countryRatePair = response.data.rates;
        this.countries = Object.keys(response.data.rates);
        // 1 euro can buy that many moneys 
        this.rates =  Object.values(response.data.rates);
      })
      .catch(error => { // Executes if an error occurs if code is not >= 200 && < 300
        this.showError = true;
      })
  },
}
</script>
