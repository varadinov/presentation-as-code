<script setup lang="ts">
import { ref, onMounted } from 'vue'

const weather = ref<{
  temperature: number
  description: string
  wind: number
} | null>(null)

const isLoading = ref(true)
const error = ref<string | null>(null)

const fetchWeather = async () => {
  const latitude = 42.6977
  const longitude = 23.3219
  const apiUrl = `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true`

  try {
    const response = await fetch(apiUrl)
    if (!response.ok) throw new Error('Failed to fetch weather data')

    const data = await response.json()
    console.log(data)
    weather.value = {
      temperature: data.current_weather.temperature,
      description: data.current_weather.weathercode,
      wind: data.current_weather.windspeed
    }
  } catch (err) {
    error.value = (err as Error).message
  } finally {
    isLoading.value = false
  }
}

onMounted(fetchWeather)
</script>

<template>
  <div class="weather-container">
    <h2>Weather in Sofia</h2>

    <div v-if="isLoading">Loading...</div>
    <div v-else-if="error">Error: {{ error }}</div>
    <div v-else>
      <p>🌡 Temperature: {{ weather?.temperature }}°C</p>
      <p>🌤 Condition: {{ weather?.description }}</p>
      <p>💨 Windspeed: {{ weather?.wind }}km/h</p>
    </div>
  </div>
</template>

<style scoped>
.weather-container {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 16px;
  width: 300px;
  font-family: Arial, sans-serif;
}

h2 {
  margin-top: 0;
}

p {
  margin: 8px 0;
}
</style>
