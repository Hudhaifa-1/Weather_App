<script setup>
import { ref } from 'vue'
import SearchInput from './components/SearchInput.vue'
import WeatherCard from './components/WeatherCard.vue'
import Footer from './components/Footer.vue'

const places = ref([])
const error_message = ref('There are no matching places, try writing more letters!')

const addPlace = (data) => {
  const place_lat = data.location.lat
  if (places.value.length > 0) {
    places.value.map((p) => {
      if (place_lat !== p.location.lat) places.value.push(data)
    })
  } else {
    places.value.push(data)
  }
}

const setErrorMessage = (data) => {
  error_message.value = data;
}


const deletePlace = (deleted_place_lat) => {
  places.value = places.value.filter((p) => p.location.lat != deleted_place_lat)
  if(places.value.length <= 0) location.reload()
}
</script>
<template>
  <main class="min-h-[570px] h-full">
    <!-- Date -->
    <div class="text-center mb-6">
      {{
        new Date().toLocaleDateString('en-us', {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric'
        })
      }}
    </div>

    <!-- Search input -->
    <SearchInput class="mb-2" @place-data="addPlace"  @error-message="setErrorMessage" />

    <!-- Weather cards -->
    <div class="grid md:grid-cols-2 gap-2">
      <div v-for="(place, idx) in places" :key="idx">
        <WeatherCard
          :place="place"
          :days="place.forecast.forecastday"
          @delete-place="deletePlace"
        />
      </div>
    </div>
  </main>
  <Footer class=" bottom-[1px]" />


</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;1,100;1,200;1,300;1,400;1,500&display=swap');
*{
  font-family: 'Poppins', sans-serif;
}
</style>
