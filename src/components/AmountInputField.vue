<template>
  <v-form v-model="valid">
    <v-container>
      <v-layout>
        <v-flex
        >
          <v-text-field
            color="#8c8c8c"
            v-model="amount"
            :rules="amountRules"
            type="number"
            label="Enter Amount"
            required
            @keyup="setAmount"
          ></v-text-field>
        </v-flex>
      </v-layout>
    </v-container>
  </v-form>
</template>

<script>
  export default {
    name: 'AmountInputField',
    
    data: () => ({
      amount: '',
      valid: false,
      amountRules: [
        v => !!v || 'Amount is required (integers only!)',
      ],
    }),

    methods: {
      setAmount(value) {
        this.$emit('amountSet', value.srcElement.value);
      }
    }
  }
</script>