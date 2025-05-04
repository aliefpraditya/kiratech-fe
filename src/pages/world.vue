<template>
  <v-container>
    <v-card>
      <v-card-title>
        <v-text-field
          v-model="newCountry"
          label="Country"
          outlined
          dense
          class="mr-4"
        ></v-text-field>
        <v-text-field
          v-model="newCapital"
          label="Capital City"
          outlined
          dense
          class="mr-4"
        ></v-text-field>
        <v-btn color="primary" @click="addRow">Add</v-btn>
      </v-card-title>
      <v-data-table :headers="headers" :items="countries" class="elevation-1" s>
        <template v-slot:top>
          <v-toolbar flat>
            <v-toolbar-title>List of Countries</v-toolbar-title>
          </v-toolbar>
        </template>
        <template v-slot:body="{ items }">
          <tr v-for="(item, index) in items" :key="index">
            <td>{{ item.country }}</td>
            <td>{{ item.capital }}</td>
          </tr>
        </template>
      </v-data-table>
    </v-card>
  </v-container>
</template>

<script lang="ts" setup>
const headers = [
  { title: "Country", key: "country", sortable: true },
  { title: "Capital City", key: "capital" },
];
const countries = ref([
  { country: "United States", capital: "Washington, D.C." },
  { country: "Canada", capital: "Ottawa" },
  { country: "United Kingdom", capital: "London" },
  { country: "France", capital: "Paris" },
  { country: "Germany", capital: "Berlin" },
  { country: "Japan", capital: "Tokyo" },
  { country: "Australia", capital: "Canberra" },
  { country: "India", capital: "New Delhi" },
  { country: "Brazil", capital: "Brasília" },
  { country: "South Africa", capital: "Pretoria" },
]);

import { ref } from "vue";

const newCountry = ref("");
const newCapital = ref("");

const addRow = () => {
  console.log("add row", newCountry.value, newCapital.value);
  if (newCountry.value && newCapital.value) {
    countries.value.push({
      country: newCountry.value,
      capital: newCapital.value,
    });
    newCountry.value = "";
    newCapital.value = "";
  }
};
</script>
