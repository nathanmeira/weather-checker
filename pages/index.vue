<template>
  <div class="mx-auto max-w-xl h-screen flex items-center justify-center h-screen px-6 lg:px-8">
    <div class="mx-auto grid max-w-md gap-8">
      <p class="font-bold text-4xl text-center">Check the weather in</p>
      <Search @city-selected="checkWeather($event)"/>
      <p v-if="fetchError" class="text-sm text-center text-red-500"> {{ fetchError }} </p>
      <div v-if="weatherInfo" class="flex flex-col justify-between rounded-3xl bg-white p-8 shadow-xl ring-1 sm:p-10">
        <div>
          <p class="text-small leading-7 text-light-gray">It's <small class="text-base font-semibold leading-7 text-gray-600 capitalize">{{ weatherCondition }}</small></p>
          <div class="mt-4 flex items-baseline gap-x-2">
            <span class="text-9xl text-center font-bold tracking-tight text-gray-900">{{ weatherTemp }}</span>
          </div>
          <p class="mt-6 text-base leading-7 text-gray-600">{{ weatherDescription }}</p>
        </div>
      </div>
      <div class="text-center text-gray-500 text-sm mt-8">
        Powered by <a class="hover:text-gray-700 hover:underline" href="https://openweathermap.org/" target="_blank" rel="noopener">OpenWeatherAPI</a>
      </div>
    </div>
  </div>
</template>


<script>
import {capitalize} from "eslint-plugin-vue/lib/utils/casing";

export default {
  data() {
    return {
      weatherData: {},
      weatherGraphic: '',
      fetchError: '',
    }
  },
  computed: {
    weatherInfo() {
      return this.weatherId && this.weatherMain && this.weatherDescription && this.weatherTemp && this.weatherCity
    },
    weatherId() {
      return this.weatherData.weather ? this.weatherData.weather[0].id : ''
    },
    weatherCity() {
      return this.weatherData.name ? this.weatherData.name : ''
    },
    weatherMain() {
      return this.weatherData.weather ? this.weatherData.weather[0].main : ''
    },
    weatherCondition() {
      let condition = this.transformCondition();
      if (condition === 'atmospheric') {
        condition = `Atmospheric: ${this.weatherMain}`
      }
      return condition
    },
    weatherDescription() {
      return this.weatherData.weather ? capitalize(this.weatherData.weather[0].description) : ''
    },
    weatherTemp() {
      return this.weatherData.main ? (Math.round(this.weatherData.main.temp)+ ' ºC') : ''
    },
  },
  methods: {
    async checkWeather(city) {
      try {
        const API_KEY = 'YOUR_API_KEY';
        const URL = `https://api.openweathermap.org/data/2.5/weather?id=${city}&units=metric&appid=${API_KEY}`;
        const response = await fetch(URL);
        if (!response.ok) {
          throw new Error('Sorry, something went wrong. Please try again later.');
        }
        this.weatherData = await response.json();
      } catch (error) {
        this.fetchError = error.message;
        console.error('Error fetching weather data:', error);
      }
    },
    transformCondition() {
      const weatherId = this.weatherId;
      if (weatherId >= 200 && weatherId < 300) {
        return 'stormy';
      }
      if (weatherId >= 300 && weatherId < 400) {
        return 'drizzly';
      }
      if (weatherId >= 500 && weatherId < 600) {
        return 'rainy';
      }
      if (weatherId >= 600 && weatherId < 700) {
        return 'snowy';
      }
      if (weatherId >= 700 && weatherId < 800) {
        return `atmospheric`;
      }
      if (weatherId === 800) {
        return 'clear';
      }
      if (weatherId > 800) {
        return 'cloudy';
        }
    }
  }
}
</script>
