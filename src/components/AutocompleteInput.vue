<template>
  <v-card class="currency-name-container">
    <v-autocomplete
      v-model="model"
      :items="countriesList"
      :label="currency"
      persistent-hint
    >
      <template v-slot:append-outer>
        <v-slide-x-reverse-transition
          mode="out-in"
        >
          <v-icon
            :key="`icon-${isEditing}`"
            :color="isEditing ? 'success' : 'info'"
            @click="isEditing = !isEditing"
            v-text="isEditing ? 'mdi-check-outline' : 'mdi-circle-edit-outline'"
          ></v-icon>x
        </v-slide-x-reverse-transition>
      </template>
    </v-autocomplete>
  </v-card>
</template>

<style src="./autocomplete-field.css"></style>

<script>
import axios from 'axios';

  export default {
    name: 'AutocompleteInput',
    data () {
      return {
        isEditing: true,
        model: null,
      }
    },
    props: {
      currency: String,
      countriesList: Array,
    },
  }
</script>