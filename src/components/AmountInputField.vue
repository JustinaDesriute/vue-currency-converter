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
            min="1"
            label="Enter Amount"
            required
            value="123"
            onkeypress="return event.charCode >= 48"
            @input="setAmount"
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
      amount: 100,
      valid: true,
      amountRules: [
        v => !!v || 'Amount is required (integers only!)',
      ],
    }),

    methods: {
      setAmount(value) {
        console.log('value', value);
        this.$emit('amountSet', value);
      }
    }
  }
</script>