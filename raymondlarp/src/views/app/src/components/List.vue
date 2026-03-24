<script setup>

import { ref, onMounted } from "vue"

const data = ref([])
const error = ref(null)

const url = "https://data.cityofnewyork.us/resource/25th-nujf.json"

onMounted(async () => {
  try {
    const response = await fetch(url)
    data.value = await response.json()
  } catch (err) {
    error.value = "Failed to load data"
    console.error(err)
  }
})
</script>

<template>
  <h1>NYC Baby Names</h1>

  <p v-if="error">{{ error }}</p>

  <table v-if="data.length">
    <thead>
      <tr>
        <th>Year</th>
        <th>Gender</th>
        <th>Ethnicity</th>
        <th>Name</th>
        <th>Count</th>
        <th>Rank</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in data" :key="index">
        <td>{{ item.brth_yr }}</td>
        <td>{{ item.gndr }}</td>
        <td>{{ item.ethcty }}</td>
        <td>{{ item.nm }}</td>
        <td>{{ item.cnt }}</td>
        <td>{{ item.rnk }}</td>
      </tr>
    </tbody>
  </table>
</template>

<style scoped>
table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  border: 1px solid #ccc;
  padding: 8px;
  transition: background-color 0.2s ease;
}

th {
  background-color: #2c3e50;
  color: white;
}
td:hover {
  background-color: #e2e8f0;  
}
</style>
