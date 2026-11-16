<template>
  <div class="weather-dashboard d-flex align-center justify-center pa-4">
    <v-container max-width="640">
      
      <!-- Top Bar: App Title & Search -->
      <v-row align="center" class="mb-4">
        <v-col cols="12" sm="5" class="pb-0 pb-sm-3">
          <div class="d-flex align-center">
            <v-avatar color="#FF6B00" size="38" class="me-3 elevation-4">
              <v-icon icon="mdi-weather-partly-sunny" color="white" size="22"></v-icon>
            </v-avatar>
            <div>
              <h1 class="text-h6 font-weight-bold text-white lh-1">SkyPulse</h1>
              <span class="text-caption text-grey">Live Weather Monitor</span>
            </div>
          </div>
        </v-col>

        <v-col cols="12" sm="7">
          <v-form @submit.prevent="searchLocation">
            <v-text-field
              v-model="searchQuery"
              placeholder="Search city..."
              prepend-inner-icon="mdi-map-marker-outline"
              append-inner-icon="mdi-magnify"
              @click:append-inner="searchLocation"
              variant="solo-filled"
              density="compact"
              flat
              hide-details
              rounded="lg"
              class="dark-search-input"
              :loading="loading"
              bg-color="#1E2028"
            ></v-text-field>
          </v-form>
        </v-col>
      </v-row>

      <!-- Error Alert -->
      <v-slide-y-transition>
        <v-alert
          v-if="errorMessage"
          type="error"
          variant="tonal"
          density="compact"
          class="mb-4 rounded-lg"
          closable
          @click:close="errorMessage = ''"
        >
          {{ errorMessage }}
        </v-alert>
      </v-slide-y-transition>

      <!-- Bento Grid Main Layout -->
      <v-row>
        <!-- Left/Top Main Temperature Card -->
        <v-col cols="12" md="7">
          <v-card class="bento-card hero-bento pa-6 h-100 d-flex flex-column justify-space-between" rounded="xl" flat>
            <div class="d-flex justify-space-between align-start">
              <div>
                <v-chip size="small" color="#FF6B00" variant="flat" class="font-weight-bold text-white mb-2">
                  CURRENT WEATHER
                </v-chip>
                <h2 class="text-h4 font-weight-bold text-white">{{ locationName }}</h2>
                <p class="text-body-2 text-grey-lighten-1">{{ countryName }}</p>
              </div>
              <div class="icon-badge pa-3 rounded-xl">
                <v-icon :icon="currentWeather.icon" size="48" color="#FF6B00"></v-icon>
              </div>
            </div>

            <div class="d-flex align-end justify-space-between mt-6">
              <div>
                <div class="temp-display font-weight-bold text-white">
                  {{ currentWeather.temp }}<span class="text-h4 text-orange-accent-3">°C</span>
                </div>
                <div class="text-subtitle-1 font-weight-medium text-grey-lighten-2">
                  {{ currentWeather.condition }}
                </div>
              </div>
              <div class="text-right text-caption text-grey">
                Updated just now
              </div>
            </div>
          </v-card>
        </v-col>

        <!-- Right Side Stacked Metrics -->
        <v-col cols="12" md="5">
          <v-row class="h-100">
            <v-col cols="4" md="12" class="pb-md-2">
              <v-card class="bento-card pa-4 d-flex flex-column flex-md-row align-center justify-space-between h-100" rounded="xl" flat>
                <div class="d-flex align-center mb-2 mb-md-0">
                  <v-avatar color="rgba(255, 107, 0, 0.12)" size="36" class="me-md-3">
                    <v-icon icon="mdi-water-outline" color="#FF6B00" size="20"></v-icon>
                  </v-avatar>
                  <span class="text-caption text-grey-lighten-1 d-none d-md-inline">Humidity</span>
                </div>
                <div class="text-center text-md-right">
                  <div class="text-body-1 font-weight-bold text-white">{{ currentWeather.humidity }}%</div>
                  <div class="text-caption text-grey d-md-none">Humidity</div>
                </div>
              </v-card>
            </v-col>

            <v-col cols="4" md="12" class="py-md-2">
              <v-card class="bento-card pa-4 d-flex flex-column flex-md-row align-center justify-space-between h-100" rounded="xl" flat>
                <div class="d-flex align-center mb-2 mb-md-0">
                  <v-avatar color="rgba(255, 107, 0, 0.12)" size="36" class="me-md-3">
                    <v-icon icon="mdi-weather-windy" color="#FF6B00" size="20"></v-icon>
                  </v-avatar>
                  <span class="text-caption text-grey-lighten-1 d-none d-md-inline">Wind Speed</span>
                </div>
                <div class="text-center text-md-right">
                  <div class="text-body-1 font-weight-bold text-white">{{ currentWeather.windSpeed }} <small class="text-grey">km/h</small></div>
                  <div class="text-caption text-grey d-md-none">Wind</div>
                </div>
              </v-card>
            </v-col>

            <v-col cols="4" md="12" class="pt-md-2">
              <v-card class="bento-card pa-4 d-flex flex-column flex-md-row align-center justify-space-between h-100" rounded="xl" flat>
                <div class="d-flex align-center mb-2 mb-md-0">
                  <v-avatar color="rgba(255, 107, 0, 0.12)" size="36" class="me-md-3">
                    <v-icon icon="mdi-gauge" color="#FF6B00" size="20"></v-icon>
                  </v-avatar>
                  <span class="text-caption text-grey-lighten-1 d-none d-md-inline">Pressure</span>
                </div>
                <div class="text-center text-md-right">
                  <div class="text-body-1 font-weight-bold text-white">{{ currentWeather.pressure }} <small class="text-grey">hPa</small></div>
                  <div class="text-caption text-grey d-md-none">Pressure</div>
                </div>
              </v-card>
            </v-col>
          </v-row>
        </v-col>
      </v-row>

      <!-- Bottom 3-Day Forecast Section -->
      <div class="mt-6">
        <div class="d-flex align-center justify-space-between mb-3 px-1">
          <span class="text-subtitle-2 font-weight-bold text-uppercase text-grey-lighten-1 letter-spacing-1">
            3-Day Outlook
          </span>
          <v-divider class="ms-4 border-opacity-15"></v-divider>
        </div>

        <v-row>
          <v-col cols="4" v-for="(day, index) in forecast" :key="index">
            <v-card class="bento-card forecast-item pa-4 text-center" rounded="xl" flat>
              <div class="text-caption font-weight-bold text-orange-accent-3 text-uppercase mb-2">
                {{ day.day }}
              </div>
              <v-icon :icon="day.icon" size="34" color="white" class="my-2"></v-icon>
              <div class="text-h6 font-weight-bold text-white mt-1">
                {{ day.temp }}°
              </div>
            </v-card>
          </v-col>
        </v-row>
      </div>

    </v-container>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const searchQuery = ref('San Fernando')
const locationName = ref('San Fernando')
const countryName = ref('Philippines')
const loading = ref(false)
const errorMessage = ref('')

const currentWeather = ref({
  temp: 0,
  condition: 'Loading...',
  icon: 'mdi-weather-partly-cloudy',
  humidity: 0,
  windSpeed: 0,
  pressure: 1012,
})

const forecast = ref([])

const getWeatherDetails = (code) => {
  switch (code) {
    case 0:
      return { condition: 'Clear Sky', icon: 'mdi-weather-sunny' }
    case 1:
    case 2:
    case 3:
      return { condition: 'Partly Cloudy', icon: 'mdi-weather-partly-cloudy' }
    case 45:
    case 48:
      return { condition: 'Foggy', icon: 'mdi-weather-fog' }
    case 51:
    case 53:
    case 55:
    case 56:
    case 57:
      return { condition: 'Drizzle', icon: 'mdi-weather-rainy' }
    case 61:
    case 63:
    case 65:
    case 66:
    case 67:
      return { condition: 'Rainy', icon: 'mdi-weather-pouring' }
    case 71:
    case 73:
    case 75:
    case 77:
      return { condition: 'Snowy', icon: 'mdi-weather-snowy' }
    case 95:
    case 96:
    case 99:
      return { condition: 'Thunderstorm', icon: 'mdi-weather-lightning' }
    default:
      return { condition: 'Fair', icon: 'mdi-weather-sunny' }
  }
}

const getDayName = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleDateString('en-US', { weekday: 'short' })
}

const searchLocation = async () => {
  if (!searchQuery.value.trim()) return
  loading.value = true
  errorMessage.value = ''

  try {
    const geoRes = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(searchQuery.value)}&count=1`)
    const geoData = await geoRes.json()

    if (!geoData.results || geoData.results.length === 0) {
      errorMessage.value = 'Location not found. Please try another city.'
      loading.value = false
      return
    }

    const { latitude, longitude, name, country } = geoData.results[0]
    locationName.value = name
    countryName.value = country || ''

    const weatherRes = await fetch(
      `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current=temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m,surface_pressure&daily=weather_code,temperature_2m_max&timezone=auto`
    )
    const weatherData = await weatherRes.json()

    const current = weatherData.current
    const details = getWeatherDetails(current.weather_code)
    currentWeather.value = {
      temp: Math.round(current.temperature_2m),
      condition: details.condition,
      icon: details.icon,
      humidity: current.relative_humidity_2m,
      windSpeed: current.wind_speed_10m,
      pressure: current.surface_pressure || 1012,
    }

    const daily = weatherData.daily
    const tempForecast = []
    for (let i = 1; i <= 3; i++) {
      if (daily.time[i]) {
        const dayDetails = getWeatherDetails(daily.weather_code[i])
        tempForecast.push({
          day: getDayName(daily.time[i]),
          icon: dayDetails.icon,
          temp: Math.round(daily.temperature_2m_max[i]),
        })
      }
    }
    forecast.value = tempForecast

  } catch (err) {
    console.error(err)
    errorMessage.value = 'Failed to fetch weather data. Please check your internet connection.'
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  searchLocation()
})
</script>

<style scoped>
.weather-dashboard {
  background-color: #121318;
  min-height: 100vh;
}

.bento-card {
  background-color: #1C1E26 !important;
  border: 1px solid rgba(255, 255, 255, 0.06);
  transition: all 0.25s ease;
}

.hero-bento {
  background: linear-gradient(145deg, #222530 0%, #181A20 100%) !important;
  border: 1px solid rgba(255, 107, 0, 0.25);
}

.icon-badge {
  background-color: rgba(255, 107, 0, 0.12);
  border: 1px solid rgba(255, 107, 0, 0.25);
}

.temp-display {
  font-size: 4.5rem;
  line-height: 1;
  letter-spacing: -2px;
}

.forecast-item:hover {
  border-color: #FF6B00;
  transform: translateY(-4px);
  background-color: #232631 !important;
}

.dark-search-input :deep(.v-field) {
  border: 1px solid rgba(255, 255, 255, 0.08);
  color: white !important;
}

.dark-search-input :deep(.v-field--focused) {
  border-color: #FF6B00 !important;
}

.dark-search-input :deep(input::placeholder) {
  color: #7A7F8C !important;
  opacity: 1;
}

.dark-search-input :deep(input) {
  color: white !important;
}

.dark-search-input :deep(.v-icon) {
  color: #FF6B00 !important;
}

.lh-1 {
  line-height: 1.1;
}

.letter-spacing-1 {
  letter-spacing: 1px;
}
</style>

