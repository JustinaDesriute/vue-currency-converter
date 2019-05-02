<template>
  <v-card class="currency-name-container" color="rgba(241, 66, 63, 0.7)">
    <v-autocomplete
      color="#8c8c8c"
      v-model="currencyName"
      :items="countriesList"
      :label="currency"
      @change="currencySelected"
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
          ></v-icon>
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
      }
    },
    props: {
      currency: String,
      countriesList: Array,
      currencyName: String,
    },
    methods: {
      currencySelected(value) {
        const id = this.$el.id;
        this.$emit('currencySelected', value, id);
      }
    }
  }
</script>