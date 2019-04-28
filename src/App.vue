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
        <AutocompleteInput id="convertFrom" class="currency-list-item" v-on:currencySelected="setCurrencyRate" currency="Convert From" :countriesList="countries"/>
        <AutocompleteInput id="convertTo" class="currency-list-item" v-on:currencySelected="setCurrencyRate" currency="Convert To" :countriesList="countries"/>
        <AmountInputField class="currency-list-item" v-on:amountSet="setEnteredAmount" />
        <v-btn class="currency-list-item" @click="convertCurrency">convert</v-btn>
        <v-text-field class="currency-list-item" 
            placeholder="converted amount"
            :value="convertedResult"
            outline
            readonly
        ></v-text-field>
      </div>
      <DatePicker v-on:calendarDateChanged="changeDateParameter"/>
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
      requestedDay: 'initial value',
    }
  },
  props: {
    currency: String,
    enteredAmount: Number,
    currencyRate: Number,
    convertFromCurrencyRate: Number,
    convertToCurrencyRate: Number,
    newRequestedValue: String
  },

  methods: {
    setEnteredAmount(value) {
      this.enteredAmount = value;
      console.log('setEnteredAmount function value', this.enteredAmount);
    },

    setCurrencyRate(value, id) {
      console.log('setCurrencyRate beginning function value', this.enteredAmount);
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
      console.log('setCurrencyRate end function value', this.enteredAmount);
    },

    convertCurrency() {
      console.log('CONVERTING', this.enteredAmount, this.convertFromCurrencyRate, this.convertToCurrencyRate);  
      this.convertedResult = this.enteredAmount * parseInt(this.convertFromCurrencyRate) * parseInt(this.convertToCurrencyRate);
    },

    changeDateParameter(value) {
      this.newRequestedValue = value;
      console.log('this.newRequestedValue', this.newRequestedValue);
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
        
        if (day < 10) { let day = '0' + day; }; 
        if(month < 10) { month = '0' + month; };

        this.requestedDay = year + '-' + month + '-' + day; 
      }
      else {this.requestedDay = this.newRequestedValue}
    },

    callExchangeRatesApi() {
      axios
        .get('https://api.exchangeratesapi.io/' + this.requestedDay)
        .then(response => {
          console.log('response from API', response);
          this.countryRatePair = response.data.rates;
          this.base =  response.data.base;
          this.date =  response.data.date;
          this.countries =  Object.keys(response.data.rates);
          this.rates =  Object.values(response.data.rates);
          this.convertedResult = 0;
        })
        .catch(error => { // Executes if an error occurs if code is not >= 200 && < 300
        // show the snackbar!
          this.showError = true;
        })
    }
  },

  mounted() {
    this.setDateParameter();
    this.callExchangeRatesApi();
  },
}
</script>
