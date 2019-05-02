<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span class="font-weight-light">currency converter</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <span>Exchange rates updated on: <span class="text-color">{{ lastUpdateDate }}</span> by the European Central Bank</span>
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
        <AmountInputField id="amountInputField" class="currency-list-item" v-on:amountSet="setEnteredAmount" />
        <AutocompleteInput id="convertFrom" class="currency-list-item" v-on:currencySelected="setCurrencyRate(...arguments)" currency="Convert From" :countriesList="countries" :currencyName="baseCurrency"/>
        <v-btn color="red" class="currency-list-item" @click="swapCurrencies">&#8633;</v-btn>
        <AutocompleteInput id="convertTo" class="currency-list-item" v-on:currencySelected="setCurrencyRate(...arguments)" currency="Convert To" :countriesList="countries" :currencyName="convertToCurrencyName"/>
        <v-text-field class="currency-list-item"
            color="#8c8c8c" 
            placeholder="converted amount"
            :value="convertedResult"
            readonly
        ></v-text-field>
        <CurrencyRatioDisplay :fromEqualsTo="fromRatioTo" :toEqualsFrom="toRatioFrom"/>
      </div>
      <ErrorHandler :showError="showSnackbar"/>
    </v-content>

    <v-footer class="pa-3">
      <v-spacer></v-spacer>
      <div>&copy; {{ new Date().getFullYear() }} check out my <a href="https://github.com/JustinaDesriute/" target="_blank">GitHub profile</a> and contact me on <a href="https://www.linkedin.com/in/justina-de%C5%A1ri%C5%ABt%C4%97-966153101/" target="_blank">LinkedIn</a> </div>
    </v-footer>
  </v-app>
</template>

<style src="./components/layout-styles.css"></style>

<script>
import AutocompleteInput from './components/AutocompleteInput'
import DatePicker from './components/DatePicker'
import ErrorHandler from './components/ErrorHandler'
import AmountInputField from './components/AmountInputField'
import CurrencyRatioDisplay from './components/CurrencyRatioDisplay'

import axios from 'axios';

export default {
  name: 'App',
  components: {
    AutocompleteInput,
    DatePicker,
    CurrencyRatioDisplay,
    ErrorHandler,
    AmountInputField,
  },
  data () {
    return {
      baseCurrency: 'GBP',
      convertToCurrencyName: 'USD',
      lastUpdateDate: '...',
      convertedResult: '',
      countries: Array,
      currency: String,
      requestedDay: 'latest',
      showSnackbar: false,
      initialLoad: true,
    }
  },
  props: {
    fromRatioTo: String,
    toRatioFrom: String,
    enteredAmount: Number,
    currencyRate: Number,
    calculatedRate: Number,
    convertToCurrencyRate: Number,
    swappedConversionRate: Number,
    newRequestedValue: String,
  },

  methods: {
    setEnteredAmount(value) {
      if (value == undefined && this.initialLoad == true) {
        value = 100;
      } else if (value == undefined && this.enteredAmount != undefined) {
        value = this.enteredAmount;
      }
      this.enteredAmount = value;
      this.convertCurrency();
    },

    swapCurrencies() {
      let valuePlaceholder = this.baseCurrency;
      this.baseCurrency = this.convertToCurrencyName;
      this.convertToCurrencyName = valuePlaceholder;
      this.setCurrencyRate(this.baseCurrency, 'swap');
    },

    calculateRate(value) {
      let currencyRate = Object.keys(this.countryRatePair)
        .filter(key => key == value)    
        .reduce((obj, key) => {
          obj[key] = this.countryRatePair[key];
          return parseFloat(Object.values(obj));
        }, {});

        this.calculatedRate = currencyRate;
    },

    setCurrencyRate(value, id) {
      if (this.initialLoad == true) { return };
      this.calculateRate(value);      

      if (id == ' ') {
        this.calculateRate(this.convertToCurrencyName);
        this.convertToCurrencyRate = this.calculatedRate;
      } else if (id == 'swap') {
          this.baseCurrency = value;
          this.callExchangeRatesApi();
      } else if (id == 'convertTo') {
          this.convertToCurrencyName = value;
          this.calculateRate(value);
          this.convertToCurrencyRate = this.calculatedRate;
      } else if (id == 'convertFrom') {
          this.baseCurrency = value;
          this.callExchangeRatesApi();
      } 
      this.convertCurrency();
    },

    convertCurrency() {
      // type check needed for initial conversion
      if (typeof(this.convertToCurrencyRate) == 'object' || this.convertToCurrencyRate == undefined) {
        this.convertToCurrencyRate = 1.3006678416;
      }
      if (typeof(this.enteredAmount) == 'undefined') {
        this.enteredAmount = 100;
      }

      this.setComparisonFieldValues(this.convertToCurrencyName);
      this.convertedResult = (parseFloat(this.enteredAmount) * parseFloat(this.convertToCurrencyRate)).toFixed(2);
    },

    swapConversionRates(value) {
      axios
        .get('https://api.exchangeratesapi.io/' + this.requestedDay + '?base=' + this.baseCurrency)
        .then(response => {
          this.pair = response.data.rates;

          let swappedCurrencyRate = Object.keys(this.pair)
            .filter(key => key == value)    
            .reduce((obj, key) => {
              obj[key] = this.pair[key];
              return parseFloat(Object.values(obj));
            }, {});

            this.swappedConversionRate = swappedCurrencyRate;
            this.fromRatioTo = '1' + this.convertToCurrencyName + ' = ' + this.swappedConversionRate.toFixed(4) + ' ' + this.baseCurrency;
        })
        .catch(error => {
          console.log('error', error);
          this.showSnackbar = true;
        })
    },

    setComparisonFieldValues(value) {
      this.swapConversionRates(value);
      this.toRatioFrom = '1' + this.baseCurrency + ' = ' + this.convertToCurrencyRate.toFixed(4) + ' ' + this.convertToCurrencyName;
    },

    changeDateParameter(value) {
      this.newRequestedValue = value;
      this.setDateParameter();
      this.callExchangeRatesApi();
    },

    setDateParameter() {
      if ((this.requestedDay == 'latest') && (this.newRequestedValue == undefined)) {
        return;
      } 
        this.requestedDay = this.newRequestedValue;
    },

    callExchangeRatesApi() {
      axios
        .get('https://api.exchangeratesapi.io/' + this.requestedDay + '?base=' + this.baseCurrency)
        .then(response => {
          this.setEnteredAmount();
          this.countryRatePair = response.data.rates;
          this.lastUpdateDate =  response.data.date;
          this.countries =  Object.keys(response.data.rates);
          this.setCurrencyRate(this.baseCurrency, ' ');
          this.initialLoad = false;
        })
        .catch(error => {
          console.log('error', error);
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
