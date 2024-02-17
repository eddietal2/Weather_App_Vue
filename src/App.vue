<script setup lang="ts">
import { onMounted, ref } from 'vue';

// Navbar Functionality
var isDet = ref(true);
var isSA = ref(false);
var isToyko = ref(false);

function toDet() {
  isDet.value = true;
  isSA.value = false;
  isToyko.value = false;
}
function toSA() {
  isDet.value = false;
  isSA.value = true;
  isToyko.value = false;
}
function toTokyo() {
  isDet.value = false;
  isSA.value = false;
  isToyko.value = true;
}

var sevenDayForecast = ref([]);
function getWeather() {
  // Calculating the size and position of wrapper and forecast information
  const forecastWrapper = document.getElementById("forecast-wrapper")!;
  const distanceToBottom = window.innerHeight - forecastWrapper.getBoundingClientRect().bottom;
  console.log(distanceToBottom);
  forecastWrapper.style.height = distanceToBottom+"px";

  // Getting Weather Data
  fetch("https://api.weatherbit.io/v2.0/forecast/daily?city=Detroit,MI&key=354bd7f3d59c482dbbabdc15c07be71d")
  .then(response => response.json())
  .then(data => {
    // Access and process the HTML data returned by the API
    console.log(data.data.slice(0,7));
    sevenDayForecast.value = data.data.slice(0,7);
    
  })
  .catch(error => {
    console.error("Error fetching data:", error);
  });
}

onMounted(() => {
  getWeather()
})
</script>

<template>
  <ion-grid :class="{ picdet: isDet, picsa: isSA, pictoyko: isToyko }" class="bg-base">
    <ion-row class="ion-justify-content-center">
      <!-- <ion-col class="ion-text-left message" size="5">
        <h1>Goodmorning!</h1>
      </ion-col> -->
      <ion-col class="toolbar ion-text-right" size="11">
        <ion-button :class="isDet ? 'active' : 'inactive'" @click="toDet()">Detroit</ion-button>
        <ion-button :class="isSA ? 'active' : 'inactive'" @click="toSA()">South Africa</ion-button>
        <ion-button :class="isToyko ? 'active' : 'inactive'" @click="toTokyo()">Japan</ion-button>
      </ion-col>
    </ion-row>
    <ion-row id="forecast-wrapper" class="ion-justify-content-center ion-align-items-end">
      <ion-col v-for="(day, index) in sevenDayForecast" :key="index" class="f-day" size="1.4">
        {{ day['datetime'] }}
      </ion-col>
    </ion-row>
  </ion-grid>
</template>

<style scoped>
.bg-base {
  background-size: cover;
  background-repeat: no-repeat;
  width: 100%;
  height: 100vh;
}
.toolbar ion-button {
  width: 7.5rem;
  font-size: 0.7em;
  margin: 0 0.5em;
  transition: 0.5s;
}
/* .message {
  background: #ffffff81;
} */
.active {
  --background: rgb(243, 226, 226);
  color: #dd0000;
}
.inactive {

  --background: rgba(26, 0, 26, 0.389);
  color: #fff;
}
#forecast-wrapper {
  height: auto;
  background: #ae0303ad;
  /* margin-top: 35%; */
}
.f-day {
  height: 100px;
  margin: 0 0.5em;
  background: #ccc;
}
.picdet {
  background-image: url('../src/assets/images/detroit_weather.jpeg');
  animation: fade-bg-in 0.5s ease forwards;
}
.picsa {
  background-image: url('../src/assets/images/southafrica_weather.jpeg');
  animation: fade-bg-in 0.5s ease forwards;
}
.pictoyko {
  background-image: url('../src/assets/images/tokyo_weather.jpeg');
  animation: fade-bg-in 0.5s ease forwards;
}
@keyframes fade-bg-in {
  0% {
    opacity: 0;
  }
  100% {
    opacity: 1;
  }
}
.weather-data {
  background: #ccc;
  height: 100px;
  width: 80%;
}
</style>
