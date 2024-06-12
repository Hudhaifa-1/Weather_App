<script setup>
import { reactive } from 'vue'

const emit = defineEmits(['place-data', 'error-message'])
let error_message = reactive(null)
let no_result = reactive(true)
const searchTerm = reactive({
  query: '',
  timout: null,
  results: null
})

const handleSearch = () => {
  clearTimeout(searchTerm.timout)
  searchTerm.timout = setTimeout(async () => {
    if (searchTerm.query !== '') {
      let res = await fetch(
        `http://api.weatherapi.com/v1/search.json?key=d89e7b8b75904328b5975046241206&q=${searchTerm.query}`
      )

      const data = await res.json()
      if (data == '') {
        no_result = false
        error_message = 'There are no matching places, try writing more letters!'
      } else {
        no_result = false
        error_message = null
      }
      no_result = false
      searchTerm.results = data
    } else {
      searchTerm.results = null
      no_result = true
    }
  }, 500)
}

const getWeather = async (id) => {
  let res = await fetch(
    `http://api.weatherapi.com/v1/forecast.json?key=d89e7b8b75904328b5975046241206&q=id:${id}&days=3&aqi=no&alerts=no`
  )
  const data = await res.json()

  emit('place-data', data)
  searchTerm.query = ''
  searchTerm.results = null
}
</script>

<template>
  <div class="mb-5 lg:w-[60%] lg:mx-auto ">
    <form>
      <div class="bg-white border border-indigo-600/30 rounded-lg shadow-lg flex items-center">
        <i class="fa-solid fa-magnifying-glass p-2 text-indigo-600"></i>
        <input
          @input="handleSearch"
          v-model="searchTerm.query"
          type="text"
          placeholder="Search for a place"
          class="rounded-r-lg p-2 border-0 outline-0 focus:ring-2 focus:ring-indigo-600 ring-inset w-full"
        />
      </div>
    </form>
    <!-- search suggestions -->
    <div v-if="searchTerm.results !== null" class="bg-white my-2 rounded-lg shadow-lg">
      <div v-for="place in searchTerm.results" :key="place.id">
        <button
          @click="getWeather(place.id)"
          class="px-2 my-2 hover:text-indigo-600 hover:font-bold w-full text-left"
        >
          {{ place.name }}, {{ place.region }}, {{ place.country }}
        </button>
      </div>
    </div>
    <div
      v-if="no_result == false && error_message !== null && searchTerm.query !== ''"
      class="bg-white my-2 rounded-lg shadow-lg"
    >
      <div class="px-2 py-2 my-2 text-red-500 w-full text-left lg:text-center">
        {{ error_message }}
      </div>
    </div>
    <div v-if="no_result" class="bg-white my-2 rounded-lg shadow-lg">
      <div class="px-2 py-2 my-2 text-indigo-600 w-full text-left lg:text-center">No results yet..</div>
    </div>
  </div>
</template>
