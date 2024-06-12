<script setup>
import { ref } from 'vue'
import BorderLine from './BorderLine.vue'
import WeatherForecastDay from './WeatherForecastDay.vue'
import WeatherInfo from './WeatherInfo.vue'

defineProps({
  place: Object,
  days: Array
})

const emit = defineEmits(['delete-place'])

const showDetial = ref(false)
const detial_change = (data) => {
  showDetial.value = data
}

const deletePlace = (place) => {
  emit('delete-place', place.location.lat)
  showDetial.value = false
}

const convert_24hour_to_12hour = (localTimeString) => {
const localDate = new Date(localTimeString);

const hours24 = localDate.getHours();
const minutes = localDate.getMinutes();

const hours12 = hours24 % 12 || 12; // Convert 0 to 12 for midnight
const ampm = hours24 >= 12 ? 'PM' : 'AM';

const formattedMinutes = minutes.toString().padStart(2, '0');

const formattedTime = `${hours12}:${formattedMinutes} ${ampm}`;
return formattedTime;
}
</script>

<template>
  <div :class="place.current.is_day === 1? 'bg-day' : 'bg-night'" class=" p-10 rounded-lg overflow-hidden shadow-lg relative gap-6 mb-6">
    <!-- Location & time -->
    <div class="flex justify-between items-center mb-2">
      <div class="flex items-center gap-2 text-white">
        <i class="fa-solid fa-location-dot"></i>
        <h1 class="text-3xl">{{ place.location.name }}</h1>
      </div>
      <div class="flex items-center gap-2 text-white">
        <i class="fa-solid fa-clock"></i>
        <h1 class="text-3xl">
          {{convert_24hour_to_12hour(place.location.localtime)}}
        </h1>
      </div>
    </div>

    <!-- current weather -->
    <div class="flex flex-col justify-center items-center">
      <img :src="place.current.condition.icon" alt="icon" width="200" class="mx-auto -mb-10" />
      <h1 class="text-white text-9xl">
        {{ Math.round(place.current.temp_c) }}
        &deg;
      </h1>
      <p class="text-white text-2xl">{{ place.current.condition.text }}</p>
    </div>

    <BorderLine />

    <!-- forecast -->
    <div v-for="(day, idx) in days" :key="idx">
      <WeatherForecastDay :day="day" />
    </div>

    <!-- info -->
    <transition >
        <div  v-show="showDetial">
      <WeatherInfo :place="place" @hide-detial="detial_change" @remove-place="deletePlace(place)" />
    </div>
    </transition>

    <!-- forecast btn -->
    <div class="flex items-center justify-end gap-1 text-white mt-10">
      <button @click="showDetial = true">
        More <i class="fa-solid fa-arrow-right text-sm -mb-px"></i>
      </button>
    </div>
  </div>
</template>

<style>
.v-enter-active,
.v-leave-active {
  transition: opacity .2s ease-in-out;
}

.v-enter-from,
.v-leave-to {
  opacity: 0;
}

.bg-day {
  background-color: #8ec5fc;
  background-image: linear-gradient(62deg, #50a1f3 0%, #967caf 100%);
}
.bg-night {
  background-color: #07223d;
  background-image: linear-gradient(62deg, #0a2a4a 0%, #270845 100%);
}
</style>
