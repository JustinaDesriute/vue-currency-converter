<template>
    <v-flex>
        <v-text-field class="currency-list-item"
            color="#8c8c8c" 
            :value="toEqualsFrom"
            readonly
        ></v-text-field>
        <!-- <v-text-field class="currency-list-item"
            color="#8c8c8c" 
            :value="fromEqualsTo"
            readonly
        ></v-text-field> -->
    </v-flex>
</template>

<script>
  export default {
    name: 'CurrencyRatioDisplay',
    data () {
      return {
       
      }
    },
    props: {
      // fromEqualsTo: String,
      toEqualsFrom: String,
    },
  }
</script>