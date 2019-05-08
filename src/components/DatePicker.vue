<template>
  <v-date-picker :max="date" :min="min" no-title color="red" width="250" @input="datePicked" v-model="picker" :landscape="landscape"></v-date-picker>
</template>

<style src="./date-picker.css"></style>

<script>
  import 'vuetify/dist/vuetify.min.css'

  export default {
    name: 'DatePicker',
    data () {
        return {
          picker: new Date().toISOString().substr(0, 10),
          landscape: false,
          date: new Date().toISOString().substr(0,10),
          min: new Date(Date.now() - 315569260000).toISOString().substr(0,10),
        }
      },

    methods: {
      datePicked(value) {
        this.$emit('calendarDateChanged', value)
      }
    }
  }
</script>
