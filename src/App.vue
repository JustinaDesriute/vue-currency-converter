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
        <AmountInputField class="currency-list-item"/>
        <AutocompleteInput class="currency-list-item" currency="Convert From" :countriesList="countries"/>
        <AutocompleteInput class="currency-list-item" currency="Convert To" :countriesList="countries"/>
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
    }
  },
  props: {
    currency: String,
  },
  mounted() {
      let today = new Date();
      let day = today.getDate();
      let month = today.getMonth() + 1; //January is 0!
      let year = today.getFullYear();   
      
      if (day < 10) {
        let day = '0' + day;
      } 

      if(month < 10) {
        month = '0' + month;
      } 
    // if the calendar day != today --> set 'today' to the picked day

    today = year + '-' + month + '-' + day ; 
    console.log('today', today);

      axios
        .get('https://api.exchangeratesapi.io/' + today)
        .then(response => {
          console.log('full API response', response);
          this.base =  response.data.base;
          this.date =  response.data.date;
          this.countries =  Object.keys(response.data.rates);
          // 1 euro can buy that many moneys 
          this.rates =  Object.values(response.data.rates);
          this.convertedResult = 666;
          console.log('countries:', this.countries);
          console.log('convertion rate values:', this.rates);
        })
        .catch(error => { // Executes if an error occurs if code is not >= 200 && < 300
          this.showError = true;
        })
  },
}
</script>
