
<script setup lang="ts">
import TemperatureGraph from './TemperatureGraph.vue'
import { onBeforeMount, ref, computed } from 'vue'

const apiKey = '5f4b3e3b3e4b3e0001f3e4b3e'

const temperatureData = ref([])
const precipitationData = ref([])

const formData = ref({
  lat: '57.71877135991238',
  lng: '12.046323175384618',
  current: {
    temperature_2m: true,
    relative_humidity_2m: true,
    apparent_temperature: true,
    is_day: true,
    precipitation: true,
    rain: true,
    showers: true,
    snowfall: true,
    weather_code: true,
    cloud_cover: true,
    pressure_msl: true,
    surface_pressure: true,
    wind_speed_10m: true,
    wind_direction_10m: true,
    wind_gusts_10m: true
    },
  hourly: {
    temperature_2m: true,
    relative_humidity_2m: true,
    dew_point_2m: true,
    apparent_temperature: true,
    precipitation_probability: true,
    precipitation: true,
    rain: true,
    showers: true,
    snowfall: true,
    snow_depth: true
  },
  forecast_days: 10,
})

const url = computed(() => {
  return `https://api.open-meteo.com/v1/forecast?latitude=${formData.value.lat}&longitude=${formData.value.lng}&current=temperature_2m,relative_humidity_2m,apparent_temperature,is_day,precipitation,rain,showers,snowfall,weather_code,cloud_cover,pressure_msl,surface_pressure,wind_speed_10m,wind_direction_10m,wind_gusts_10m&hourly=temperature_2m,relative_humidity_2m,dew_point_2m,apparent_temperature,precipitation_probability,precipitation,rain,showers,snowfall,snow_depth,weather_code,pressure_msl,surface_pressure,cloud_cover,cloud_cover_low,cloud_cover_mid,cloud_cover_high,visibility,evapotranspiration,et0_fao_evapotranspiration,vapour_pressure_deficit,wind_speed_10m,wind_speed_80m,wind_speed_120m,wind_speed_180m,wind_direction_10m,wind_direction_80m,wind_direction_120m,wind_direction_180m,wind_gusts_10m,temperature_80m,temperature_120m,temperature_180m,soil_temperature_0cm,soil_temperature_6cm,soil_temperature_18cm,soil_temperature_54cm,soil_moisture_0_to_1cm,soil_moisture_1_to_3cm,soil_moisture_3_to_9cm,soil_moisture_9_to_27cm,soil_moisture_27_to_81cm&daily=weather_code,temperature_2m_max,temperature_2m_min,apparent_temperature_max,apparent_temperature_min,sunrise,sunset,daylight_duration,sunshine_duration,uv_index_max,uv_index_clear_sky_max,precipitation_sum,rain_sum,showers_sum,snowfall_sum,precipitation_hours,precipitation_probability_max,wind_speed_10m_max,wind_gusts_10m_max,wind_direction_10m_dominant,shortwave_radiation_sum,et0_fao_evapotranspiration&forecast_days=${formData.value.forecast_days}`
})

const getWeather = async () => {
  const data =  await fetch(url.value, {
    headers: {
      'Authorization': `Bearer ${apiKey}`
    }
  })

  return data.json()
}
onBeforeMount(async () => {
  console.log('mounted')
  const weatherData = await getWeather()
  console.log(weatherData)


  for (let i = 0; i < weatherData.hourly.time.length; i++) {
    temperatureData.value.push({
      time: new Date(weatherData.hourly.time[i]),
      temperature: weatherData.hourly.temperature_2m[i],
      apparent_temperature: weatherData.hourly.apparent_temperature[i],
    })

    precipitationData.value.push({
      time: new Date(weatherData.hourly.time[i]),
      precipitation: weatherData.hourly.precipitation[i],
      rain: weatherData.hourly.rain[i],
      snowfall: weatherData.hourly.snowfall[i],
      showers: weatherData.hourly.showers[i],
      precipitation_probability: weatherData.hourly.precipitation_probability[i]
    })
  }
console.log(temperatureData.value)
})
</script>
<template>
  <v-container fluid >
    <v-responsive class="align-center text-center fill-height">
      <v-row>
        <v-col cols="3">
          <v-form>
            <v-text-field
              v-model="formData.lat"
              label="Latitude"
              outlined
            ></v-text-field>
            <v-text-field
              v-model="formData.lng"
              label="Longitude"
              outlined
            ></v-text-field>
            <v-select
              v-model="formData.forecast_days"
              :items="[1, 3, 5, 7, 10]"
              label="Forecast Days"
              outlined
            ></v-select>
            <v-checkbox
              v-model="formData.current.temperature_2m"
              label="Temperature">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.relative_humidity_2m"
              label="Relative Humidity">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.apparent_temperature"
              label="Apparent Temperature">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.is_day"
              label="Is Day">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.precipitation"
              label="Precipitation">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.rain"
              label="Rain">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.showers"
              label="Showers">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.snowfall"
              label="Snowfall">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.weather_code"
              label="Weather Code">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.cloud_cover"
              label="Cloud Cover">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.pressure_msl"
              label="Pressure MSL">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.surface_pressure"
              label="Surface Pressure">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.wind_speed_10m"
              label="Wind Speed 10m">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.wind_direction_10m"
              label="Wind Direction 10m">
            </v-checkbox>
            <v-checkbox
              v-model="formData.current.wind_gusts_10m"
              label="Wind Gusts 10m">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.temperature_2m"
              label="Temperature">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.relative_humidity_2m"
              label="Relative Humidity">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.dew_point_2m"
              label="Dew Point">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.apparent_temperature"
              label="Apparent Temperature">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.precipitation_probability"
              label="Precipitation Probability">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.precipitation"
              label="Precipitation">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.rain"
              label="Rain">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.showers"
              label="Showers">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.snowfall"
              label="Snowfall">
            </v-checkbox>
            <v-checkbox
              v-model="formData.hourly.snow_depth"
              label="Snow Depth">
            </v-checkbox>

            <v-btn @click="getWeather">Get Weather</v-btn>
          </v-form>
        </v-col>
        <v-col cols="9">
          <v-row>
            <v-col cols="12">
              <TemperatureGraph v-if="temperatureData.length" :data="temperatureData"/>
            </v-col>
          </v-row>
          <v-row>
            <v-col cols="12">
              <PrecipitationGraph v-if="precipitationData.length" :data="precipitationData"/>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

