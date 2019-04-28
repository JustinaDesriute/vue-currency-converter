<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span class="font-weight-light">currency converter</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <span>The base currency: <span class="text-color">{{ base }}</span> on <span class="text-color">{{ date }}</span></span>
      <v-spacer></v-spacer>
      <v-tooltip bottom>
          <v-btn
            flat
            href="https://www.linkedin.com/in/justina-de%C5%A1ri%C5%ABt%C4%97-966153101/"
            target="_blank"
            slot="activator"
          >
            <span>built by: <span class="text-color">Justina Desriute</span></span>
          </v-btn>
        go to LinkedIn profile
      </v-tooltip>
    </v-toolbar>

    <v-content>
      <div class="currency-lists-container">
        <DatePicker v-on:calendarDateChanged="changeDateParameter"/>
        <AutocompleteInput id="convertFrom" class="currency-list-item" v-on:currencySelected="setCurrencyRate" currency="Convert From" :countriesList="countries"/>
        <AutocompleteInput id="convertTo" class="currency-list-item" v-on:currencySelected="setCurrencyRate" currency="Convert To" :countriesList="countries"/>
        <AmountInputField class="currency-list-item" v-on:amountSet="setEnteredAmount" />
        <v-btn class="currency-list-item" @click="convertCurrency">convert</v-btn>
        <v-text-field class="currency-list-item"
            color="#8c8c8c" 
            placeholder="converted amount"
            :value="convertedResult"
            readonly
        ></v-text-field>
      </div>
      <ErrorHandler :showError="showSnackbar"/>
    </v-content>

    <v-footer class="pa-3">
      <v-spacer></v-spacer>
      <div>&copy; {{ new Date().getFullYear() }} check out my <a href="https://github.com/JustinaDesriute/" target="_blank">GitHub</a> profile and contact me on <a href="https://www.linkedin.com/in/justina-de%C5%A1ri%C5%ABt%C4%97-966153101/" target="_blank">LinkedIn</a> </div>
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
      requestedDay: 'initial value',
      showSnackbar: false,
    }
  },
  props: {
    currency: String,
    enteredAmount: Number,
    currencyRate: Number,
    convertFromCurrencyRate: Number,
    convertToCurrencyRate: Number,
    newRequestedValue: String,
  },

  methods: {
    setEnteredAmount(value) {
      this.enteredAmount = value;
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
      this.convertedResult = ((parseFloat(this.enteredAmount) / parseFloat(this.convertFromCurrencyRate)) * parseFloat(this.convertToCurrencyRate)).toFixed(2);
    },

    changeDateParameter(value) {
      this.newRequestedValue = value;
      this.setDateParameter();
      this.callExchangeRatesApi();
    },

    setDateParameter() {
      if (this.newRequestedValue == this.requestedDay) { 
        return 
      } else if (this.requestedDay == 'initial value') {
        let requestedDay = new Date();
        let day = requestedDay.getDate();
        let month = requestedDay.getMonth() + 1;
        let year = requestedDay.getFullYear();   
        
        if (day < 10) { let day = '0' + day; } 
        if(month < 10) { month = '0' + month; }

        this.requestedDay = year + '-' + month + '-' + day; 
      }
      else {this.requestedDay = this.newRequestedValue}
    },

    callExchangeRatesApi() {
      axios
        .get('https://api.exchangeratesapi.io/' + this.requestedDay)
        .then(response => {
          this.countryRatePair = response.data.rates;
          this.base =  response.data.base;
          this.date =  response.data.date;
          this.countries =  Object.keys(response.data.rates);
          this.rates =  Object.values(response.data.rates);
          this.convertedResult = '';
        })
        .catch(error => {
          this.showSnackbar = true;
        })
    }
  },

  mounted() {
    this.setDateParameter();
    this.callExchangeRatesApi();
  },
}
</script>
