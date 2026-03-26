<script setup>
import { ref, onMounted, computed } from "vue"
import { Bar } from "vue-chartjs"
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from "chart.js"

ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

const data = ref([])
const error = ref(null)
const searchName = ref("")

const url = "https://data.cityofnewyork.us/resource/25th-nujf.json"

onMounted(async () => {
  try {
    const response = await fetch(url)
    data.value = await response.json()
  } catch (err) {
    error.value = "Failed to load data"
  }
})

const chartData = computed(() => {
  if (!searchName.value) return null

  // filter by name (case insensitive)
  const filtered = data.value.filter(
    item => item.nm.toLowerCase() === searchName.value.toLowerCase()
  )

  // group by ethnicity
  const grouped = {}

  filtered.forEach(item => {
    const ethnicity = item.ethcty
    const count = Number(item.cnt)

    if (!grouped[ethnicity]) {
      grouped[ethnicity] = 0
    }

    grouped[ethnicity] += count
  })

  return {
    labels: Object.keys(grouped),
    datasets: [
      {
        label: `Count for "${searchName.value}"`,
        data: Object.values(grouped),
        backgroundColor: "#42A5F5"
      }
    ]
  }
})

const chartOptions = {
  responsive: true
}
</script>

<template>
  <h1>Baby Name by Ethnicity</h1>

  <input
    v-model="searchName"
    placeholder="Enter a name (e.g. Sophia)"
    style="margin-bottom: 10px; padding: 6px;"
  />

  <p v-if="error">{{ error }}</p>

  <Bar
    v-if="chartData && chartData.labels.length"
    :data="chartData"
    :options="chartOptions"
  />

  <p v-else-if="searchName">No data found for that name</p>
</template>
<style scoped>

</style>