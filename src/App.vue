<template>
  <v-app>
    <v-toolbar app>
      <v-toolbar-title class="headline text-uppercase">
        <span class="font-weight-light">currency converter</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-tooltip bottom>
          <v-btn
            flat
            href="https://www.linkedin.com/in/justina-de%C5%A1ri%C5%ABt%C4%97-966153101/"
            target="_blank"
            slot="activator"
          >
            <span class="mr-2">built by: Justina Desriute</span>
          </v-btn>
        go to LinkedIn profile
      </v-tooltip>
    </v-toolbar>

    <v-content>
      <DatePicker/>
      <AutocompleteInput/>
      <AutocompleteInput/>
      <InputValidation/>
      <ErrorHandler/>
    </v-content>

    <v-footer class="pa-3">
      <v-spacer></v-spacer>
      <div>&copy; {{ new Date().getFullYear() }} contact me on LINKEDIN or check out my GitHub profile</div>
    </v-footer>
  </v-app>
</template>

<script>
import DatePicker from './components/DatePicker'
import AutocompleteInput from './components/AutocompleteInput'
import InputValidation from './components/InputValidation'
import ErrorHandler from './components/ErrorHandler'

import axios from 'axios';

export default {
  name: 'App',
  components: {
    AutocompleteInput,
    DatePicker,
    ErrorHandler,
    InputValidation,
  },
  data () {
    return {
      //
    }
  },
  mounted() {
    axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => {
        console.log('respone from api', response);
      })
      .catch(error => { // Executes if an error occurs if code is not >= 200 && < 300
        this.showError = true;
        this.error     = error;
      }),

      axios
      .get('https://api.exchangeratesapi.io/2019-04-26')
      .then(response => {
        console.log('respone from ANOTHER api', response);
      })
      .catch(error => { // Executes if an error occurs if code is not >= 200 && < 300
        this.showError = true;
        this.error     = error;
      })
  },
}
</script>
