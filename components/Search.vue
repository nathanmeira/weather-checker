<template>
  <div class="relative">
    <input v-model="userInput"
           type="text"
           placeholder="Enter a city name"
           class="block w-full px-4 py-2 leading-normal bg-white border border-gray-300 rounded-lg appearance-none focus:outline-none focus:bg-white focus:border-gray-500"
           @input="handleInput"
           @focus="handleInput"
           @keydown.up.prevent="focusPreviousResult"
           @keydown.down.prevent="focusNextResult"
    >
    <ul v-if="searchResults.length > 0"
        class="absolute z-10 w-full py-2 mt-1 text-base bg-white border border-gray-300 rounded-lg shadow-md">
      <li v-for="result in searchResults"
          :key="result.id"
          class="px-4 py-2 hover:bg-gray-100 cursor-pointer"
          @click="selectCity(result)">
        {{ result.name }}, {{ result.country }}
      </li>
    </ul>
    <p v-if="userInput" class="text-sm underline text-blue-600 flex justify-end cursor-pointer" @click="userInput = ''">Clear</p>
    <div v-if="loading" class="absolute flex top-1/2 right-2 transform -translate-y-1/2">
      <span class="block w-2 h-2 rounded-full bg-blue-500 mr-1 animate-bounce"></span>
      <span class="block w-2 h-2 rounded-full bg-blue-500 mr-1 animate-bounce200"></span>
      <span class="block w-2 h-2 rounded-full bg-blue-500 animate-bounce400"></span>
    </div>
  </div>
</template>

<script>
import cities from '~/assets/cities.json'

export default {
  data() {
    return {
      userInput: '',
      searchResults: [],
      cities,
      loading: false,
    }
  },
  computed: {
    filteredCities() {
      if (this.userInput.length < 3) {
        return []
      }
      const filtered = this.cities.filter(city => {
        return city.name.toLowerCase().startsWith(this.userInput.toLowerCase())
      })
      return filtered.slice(0, 10)
    }
  },
  methods: {
    handleInput() {
      this.loading = true
      setTimeout(() => {
        this.searchResults = this.filteredCities.map(city => {
          return { id: city.id, name: city.name, country: city.country }
        })
        this.loading = false
      }, 1000)
    },
    selectCity(city) {
      this.userInput = `${city.name}, ${city.country}`
      this.$emit('city-selected', city.id)
      this.closeDropdown()
    },
    closeDropdown() {
      this.searchResults = []
      this.focusedResultIndex = null
    },
    focusPreviousResult() {
      if (this.focusedResultIndex === null) {
        this.focusedResultIndex = this.searchResults.length - 1
      } else if (this.focusedResultIndex > 0) {
        this.focusedResultIndex--
      }
    },
    focusNextResult() {
      if (this.focusedResultIndex === null) {
        this.focusedResultIndex = 0
      } else if (this.focusedResultIndex < this.searchResults.length - 1) {
        this.focusedResultIndex++
      }
    }
  }
}
</script>

<style scoped>
  input:focus + ul {
    display: block;
  }

  .loading-dots {
    display: inline-block;
    vertical-align: middle;
    overflow: hidden;
    height: 1em;
  }

  .loading-dots > span {
    display: inline-block;
    vertical-align: middle;
    transform: translateY(0);
    animation: loading-dots 1s infinite ease-in-out;
  }

  .loading-dots > span:nth-child(1) {
    animation-delay: 0s;
  }

  .loading-dots > span:nth-child(2) {
    animation-delay: 0.2s;
  }

  .loading-dots > span:nth-child(3) {
    animation-delay: 0.4s;
  }

  @keyframes loading-dots {
    0% {
      transform: translateY(0);
    }
    50% {
      transform: translateY(-0.25em);
    }
    100% {
      transform: translateY(0);
    }
  }
</style>
