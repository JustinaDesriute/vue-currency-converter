<template>
  <v-form v-model="valid">
    <v-container>
      <v-layout>
        <v-flex
        >
          <v-text-field
            v-model="amount"
            :rules="amountRules"
            type="number"
            label="Enter Amount"
            required
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
      valid: false,
      amount: '0',
      amountRules: [
        v => !!v || 'Amount is required (integers only!)',
      ],
    })
  }
</script>