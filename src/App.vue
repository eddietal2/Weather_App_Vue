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

  getWeatherDetroit();
}
function toSA() {
  isDet.value = false;
  isSA.value = true;
  isToyko.value = false;

  getWeatherCapeTown();
}
function toTokyo() {
  isDet.value = false;
  isSA.value = false;
  isToyko.value = true;

  getWeatherTokyo();
}

var sevenDayForecast = ref([]);
const weatherIconLink = "https://www.weatherbit.io/static/img/icons/c01d.png";

function getWeatherDetroit() {
  // Clear Weather data to restart animation
  sevenDayForecast.value = [];
  // Getting Weather Data
  fetch("https://api.weatherbit.io/v2.0/forecast/daily?city=Detroit,MI&key=354bd7f3d59c482dbbabdc15c07be71d&units=I")
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
function getWeatherCapeTown() {
  // Clear Weather data to restart animation
  sevenDayForecast.value = [];
  // Getting Weather Data for Cape Town
  fetch("https://api.weatherbit.io/v2.0/forecast/daily?&lat=-33.9249&lon=18.4241&key=354bd7f3d59c482dbbabdc15c07be71d&units=I")
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
function getWeatherTokyo() {
  // Clear Weather data to restart animation
  sevenDayForecast.value = [];
  // Getting Weather Data
  fetch("https://api.weatherbit.io/v2.0/forecast/daily?&lat=35.6764&lon=139.6500&key=354bd7f3d59c482dbbabdc15c07be71d&units=I")
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
  getWeatherDetroit()
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
      <!-- <ion-col size="10">
        <h1>Forecast</h1>
      </ion-col> -->
      <ion-col 
        class="f-day" 
        size="1.4" 
        :ref="(el: any) => {console.log(el)}" 
        :key="index"
        v-for="(day, index) in sevenDayForecast" 
        >
        <span class="forecast-date">{{ (day['datetime'] as string).substring(5).replace(/-/g, "/") }}</span><br>
        <img :src="`https://www.weatherbit.io/static/img/icons/${day['weather']['icon']}.png`" alt="Weather Icon" height="70">
        <br>
        <span class="forecast-avg-temp">{{ Math.round(day['temp']) }}</span><br>
        <span class="forecast-high-temp">High - {{ Math.round(day['temp']) }}</span><br>
        <span class="forecast-low-temp">Low - {{ Math.round(day['temp']) }}</span>
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
  height: 90vh;
  /* background: #001219ad; */
  /* margin-top: 35%; */
}
.forecast-date{
  font-size: 0.8em;
}
.forecast-avg-temp {
  font-size: 2em;
}
.forecast-high-temp, .forecast-low-temp  {
  font-size: 1em;
}
.f-day {
  height: auto;
  border-radius: 10px;
  padding: 0.5em;
  margin: 0 0.5em;
  background: #fff;
  opacity: 0;
  animation: slide-forecast-day-up 0.5s ease 0.2s forwards
}
.picdet {
  background: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #000000), 
    url('../src/assets/images/detroit_weather.jpeg');
  opacity: 0;
  animation: fade-bg-in 0.5s ease forwards;
}
.picsa {
  background-image: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #000000), url('../src/assets/images/southafrica_weather.jpeg');
  animation: fade-bg-in 0.5s ease forwards;
}
.pictoyko {
  background-image: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #000000), url('../src/assets/images/tokyo_weather.jpeg');
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
@keyframes slide-forecast-day-up {
  0% {
    transform: translateY(100px);
    opacity: 0;
  }
  100% {
    transform: translateY(0px);
    opacity: 1;
  }
}
</style>
