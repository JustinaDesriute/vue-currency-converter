<template>
  <v-date-picker no-title color="red" width="250" @input="datePicked" v-model="picker" :landscape="landscape"></v-date-picker>
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
        }
      },

    methods: {
      datePicked(value) {
        this.$emit('calendarDateChanged', value)
      }
    }
  }
</script>
