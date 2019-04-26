<template>
  <v-card>
    <v-card-text>
      <v-btn
        block
        color="primary"
        dark
        @click="snackbar = true"
      >
        CLick to show Snackbar
      </v-btn>
    </v-card-text>

    <v-snackbar
      v-model="snackbar"
      :bottom="y === 'bottom'"
      :left="x === 'left'"
      :multi-line="mode === 'multi-line'"
      :right="x === 'right'"
      :timeout="timeout"
      :top="y === 'top'"
      :vertical="mode === 'vertical'"
    >
      {{ text }}
      <v-btn
        color="pink"
        flat
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </v-card>
</template>

<script>
  export default {
    name: 'ErrorHandler',
    data () {
      return {
        snackbar: false,
        y: 'top',
        x: null,
        mode: '',
        timeout: 6000,
        text: 'Sorry, data did not come through :('
      }
    }
  }
</script>