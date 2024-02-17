<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { Swiper, SwiperSlide } from 'swiper/vue';
// Import Swiper styles
import 'swiper/css';

function onSwiper(swiper: any) {
      console.log(swiper);
}
function onSlideChange() {
    console.log('slide change');
}

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
      <ion-col class="toolbar ion-text-right ion-hide-sm-down" size="11">
        <ion-button :class="isDet ? 'active' : 'inactive'" @click="toDet()">Detroit</ion-button>
        <ion-button :class="isSA ? 'active' : 'inactive'" @click="toSA()">South Africa</ion-button>
        <ion-button :class="isToyko ? 'active' : 'inactive'" @click="toTokyo()">Japan</ion-button>
      </ion-col>
      <ion-col class="toolbar-mb ion-hide-sm-up ion-text-center" size="12">
        <ion-button :class="isDet ? 'active' : 'inactive'" @click="toDet()">Detroit</ion-button>
        <ion-button :class="isSA ? 'active' : 'inactive'" @click="toSA()">South Africa</ion-button>
        <ion-button :class="isToyko ? 'active' : 'inactive'" @click="toTokyo()">Japan</ion-button>
      </ion-col>
    </ion-row>
    <ion-row id="forecast-wrapper" class="ion-justify-content-center ion-align-items-end ion-hide-sm-down">
      <!-- <ion-col size="10">
        <h1>Forecast</h1>
      </ion-col> -->
      <ion-col 
        class="f-day ion-hide-sm-down" 
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
      </ion-col>s
    </ion-row>
    <ion-row id="forecast-wrapper-mb" class="ion-justify-content-center ion-align-items-end ion-hide-sm-up">
      <swiper
        :slides-per-view="2.2"
        :space-between="10"
        @swiper="onSwiper"
        @slideChange="onSlideChange">
          <swiper-slide class="f-day-mb" :key="index" v-for="(day, index) in sevenDayForecast">
            <span class="forecast-date">{{ (day['datetime'] as string).substring(5).replace(/-/g, "/") }}</span><br>
            <img :src="`https://www.weatherbit.io/static/img/icons/${day['weather']['icon']}.png`" alt="Weather Icon" height="70">
            <br>
            <span class="forecast-avg-temp">{{ Math.round(day['temp']) }}</span><br>
            <span class="forecast-high-temp">High - {{ Math.round(day['temp']) }}</span><br>
            <span class="forecast-low-temp">Low - {{ Math.round(day['temp']) }}</span>
          </swiper-slide>
      </swiper>
    </ion-row>
  </ion-grid>
</template>

<style scoped>
/* Toolbar */
.toolbar ion-button {
  width: 7.5rem;
  font-size: 0.7em;
  margin: 0 0.5em;
  transition: 0.5s;
}
.toolbar-mb {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  bottom: 0;
  z-index: 99;
  background: #ffffff78;
  height: 4em;
}
.toolbar-mb ion-button{
  width: 7.5rem;
  font-size: 0.9em;
  margin: 0 0.5em;
  transition: 0.5s;
}
/* .message {
  background: #ffffff81;
} */
.active {
  --background: #022a86;
  color: #fff;
}
.inactive {

  --background: #555;
  color: #fff;
}

/* Forecast */
#forecast-wrapper {
  height: 90vh;
  /* background: #001219ad; */
  /* margin-top: 35%; */
}
#forecast-wrapper-mb {
  height: auto;
  position: absolute;
  width: 100%;
  bottom: 100px;
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
.f-day-mb {
  height: auto;
  border-radius: 10px;
  padding: 0.5em;
  margin: 0.5em 0em;
  background: #fff;
  opacity: 0;
  animation: slide-forecast-day-right 0.5s ease 0.2s forwards
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
@keyframes slide-forecast-day-right {
  0% {
    transform: translateX(-100px);
    opacity: 0;
  }
  100% {
    transform: translateX(0px);
    opacity: 1;
  }
}

/* Background Photos */
.bg-base {
  background-size: cover;
  background-repeat: no-repeat;
  background-position-x: 20%;
  width: 100%;
  height: 100vh;
}
.picdet {
  background: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #000000), 
    url('../src/assets/images/detroit_weather.jpeg');
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
@media (max-width: 600px) {
  .picdet {
    background: 
      linear-gradient(
        165deg,
      #00000000 40%, 
      #000000), 
      url('../src/assets/images/det_mb.png');
    background-position-x: -100px;
  }
  .picsa {
    background: 
      linear-gradient(
        165deg,
      #00000000 40%, 
      #000000), 
      url('../src/assets/images/sa_mb.png');
  }
  .pictoyko {
    background: 
      linear-gradient(
        165deg,
      #00000000 40%, 
      #000000), 
      url('../src/assets/images/tokyo_mb.png');
  }
}
</style>
