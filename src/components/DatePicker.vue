<template>
  <div>
    <v-layout row wrap>
        <v-flex xs12 sm3>
            <v-checkbox v-model="landscape" hide-details label="Landscape"></v-checkbox>
        </v-flex>
        <v-flex xs12 sm3>
            <v-checkbox v-model="reactive" hide-details label="Reactive"></v-checkbox>
        </v-flex>
    </v-layout>
    <v-date-picker v-model="picker" :landscape="landscape" :reactive="reactive"></v-date-picker>
  </div>
</template>

<script>
import 'vuetify/dist/vuetify.min.css'

export default {
  name: 'DatePicker',
  data () {
      return {
        picker: new Date().toISOString().substr(0, 10),
        landscape: true,
        reactive: false
      }
    }
}
</script>
