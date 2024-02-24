<script setup lang="ts">
import { onMounted, ref } from 'vue';
import { Swiper, SwiperSlide } from 'swiper/vue';
// Import Swiper styles
import 'swiper/css';

// Ionic Local Register
import { IonButton, IonCol, IonRow, IonGrid } from '@ionic/vue';
  import { defineComponent } from 'vue';

  defineComponent({
    components: { 
      IonButton,
      IonCol, 
      IonRow, 
      IonGrid
    },
  });

// Swiper
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
var currentLocation = 'det';
function goToWeatherBit() {
  window.open('https://www.weatherbit.io/', '_blank');
}
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

  getWeatherSA();
}
function toTokyo() {
  isDet.value = false;
  isSA.value = false;
  isToyko.value = true;

  getWeatherTokyo();
}

// Forecast
var sevenDayForecast = ref([]);
function getWeatherDetroit() {
  // Set Current Location to Detroit
  currentLocation = 'det';
  getTime(currentLocation);

  // Clear Weather data to restart animation
  sevenDayForecast.value = [];
  // Getting Weather Data
  fetch("https://api.weatherbit.io/v2.0/forecast/daily?city=Detroit,MI&key=354bd7f3d59c482dbbabdc15c07be71d&units=I")
  .then(response => response.json())
  .then(data => {
    // Access and process the HTML data returned by the API
    // console.log(data.data.slice(0,7));
    sevenDayForecast.value = data.data.slice(0,7);   
  })
  .catch(error => {
    console.error("Error fetching data:", error);
  });
}
function getWeatherSA() {
  // Set Current Location to South Africa
  currentLocation = 'sa';
  getTime(currentLocation);

  // Time Clock Slide In Animation
  const timeClockElement = document.getElementById("timeclock") as HTMLDivElement;

  console.log(timeClockElement);

  // Clear Weather data to restart animation
  sevenDayForecast.value = [];
  // Getting Weather Data for Cape Town
  fetch("https://api.weatherbit.io/v2.0/forecast/daily?&lat=-33.9249&lon=18.4241&key=354bd7f3d59c482dbbabdc15c07be71d&units=I")
  .then(response => response.json())
  .then(data => {
    // Access and process the HTML data returned by the API
    // console.log(data.data.slice(0,7));
    sevenDayForecast.value = data.data.slice(0,7);
  })
  .catch(error => {
    console.error("Error fetching data:", error);
  });
}
function getWeatherTokyo() {
  // Set Current Location to Tokyo
  currentLocation = 'tokyo';
  getTime(currentLocation);

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

// Time
var currentTime = ref('');
var currentDate = ref('');
var detInterval = ref();
var saInterval = ref();
var tokyoInterval = ref();
var clock = ref();

function getTime(location: string) {
  clearInterval(detInterval.value);
  clearInterval(saInterval.value);
  clearInterval(tokyoInterval.value);
  if(location == 'det') {
    detInterval.value = setInterval(
      () => {
        // console.log('Getting Detroit Time');
        fetch('https://worldtimeapi.org/api/timezone/America/Detroit')
          .then(response => response.json())
          .then(data => {
            const dateObject = new Date(data.datetime); 
            const formattedDate = dateObject.toLocaleDateString('en-US');
            const formattedTime = dateObject.toLocaleString('en-US', { timeZone: 'America/Detroit', hour: '2-digit', minute: '2-digit', second: '2-digit' });
            currentTime.value = formattedTime;
            currentDate.value = formattedDate;
            return;
          })
      }, 1000
    );
  }
  if(location == 'sa') {
    saInterval.value = setInterval(
      () => {
        // console.log('Getting Africa Time');
        fetch('https://worldtimeapi.org/api/timezone/Africa/Johannesburg')
          .then(response => response.json())
          .then(data => {
            const dateObject = new Date(data.datetime); 
            const formattedDate = dateObject.toLocaleDateString('en-US', {timeZone: 'Africa/Johannesburg'});
            const formattedTime = dateObject.toLocaleString('en-US', { timeZone: 'Africa/Johannesburg', hour: '2-digit', minute: '2-digit', second: '2-digit' });
            currentTime.value = formattedTime;
            currentDate.value = formattedDate;
            return;
          })
      }, 1000
    );
  }
  if(location == 'tokyo') {
    tokyoInterval.value = setInterval(
      () => {
        // console.log('Getting Tokyo Time');
        fetch('https://worldtimeapi.org/api/timezone/Asia/Tokyo')
          .then(response => response.json())
          .then(data => {
            const dateObject = new Date(data.datetime); 
            const formattedDate = dateObject.toLocaleDateString('en-US', {timeZone: 'Asia/Tokyo'});
            const formattedTime = dateObject.toLocaleString('en-US', { timeZone: 'Asia/Tokyo', hour: '2-digit', minute: '2-digit', second: '2-digit' });
            currentTime.value = formattedTime;
            currentDate.value = formattedDate;
            return;
          })
      }, 1000
    );
  }
}

// Lifecycle Hooks
onMounted(() => {
  // To Remove all the warning messages before use of the app (temporary)
  console.clear();
  getWeatherDetroit();
  getTime(currentLocation);
  
})
</script>

<template>
  <ion-grid :class="{ picdet: isDet, picsa: isSA, pictoyko: isToyko }" class="bg-base">
    <!-- Toolbars -->
    <ion-row class="ion-justify-content-center ion-align-items-center">
      <ion-col :ref="clock" class="ion-text-left clock-wrapper" size-lg="5.5" size="12">
          <p id="timeclock">{{ currentTime }}</p>
          <p id="timedate">{{ currentDate }}</p>
      </ion-col>
      <ion-col class="toolbar ion-text-right ion-hide-sm-down" size-lg="5.5">
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
    <!-- Forecasts -->
    <ion-row id="forecast-wrapper" class="ion-justify-content-center ion-align-items-end ion-hide-sm-down">
  
      <!-- Laptop Forecast -->
      <ion-col 
        class="f-day ion-hide-sm-down" 
        size="1.4" 
        :ref="(el: any) => {}" 
        :key="index"
        v-for="(day, index) in sevenDayForecast" 
        >
        <span class="forecast-date">{{ (day['datetime'] as string).substring(5).replace(/-/g, "/") }}</span><br>
        <img :src="`https://www.weatherbit.io/static/img/icons/${day['weather']['icon']}.png`" alt="Weather Icon" height="70">
        <br>
        <span class="forecast-avg-temp">{{ Math.round(day['temp']) }}</span><span style="font-size: 24px; line-height: 24px; position: relative; bottom: 5px;">&deg;</span><br>
        <span class="forecast-high-temp">Hi - {{ Math.round(day['high_temp']) }}</span><br>
        <span class="forecast-low-temp">Lo - {{ Math.round(day['low_temp']) }}</span>
      </ion-col>

      <div @click="goToWeatherBit()" class="powered-by">
          <b style="color: gold">Powered by</b>
          <span style="width: 10px"></span>
          <img height="20" src="https://cdn.weatherbit.io/static/img/logos/weatherbit/color/svg/logo-no-background.svg" alt="Weatherbit Logo">
      </div>
    </ion-row>

    <!-- Mobile Forecast -->
    <ion-row id="forecast-wrapper-mb" class="ion-hide-sm-up">
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
.v-logo {
  display: flex;
  align-items: center;
  font-size: 1em;
}
#logo-text{
  color: #fff;
}
#timeclock {
  font-size: 2em;
  background: #000d55;
  color: #fff;
  width: 200px;
  text-align: center;
  opacity: 0;
  animation: clock-slide-left 0.5s ease 2s forwards;
}
#timedate {
  font-size: 1em;
  background: #fff;
  width: 200px;
  text-align: center;
  position: relative;
  top: 0.5em;
  opacity: 0;
  animation: clock-slide-left 0.5s ease 2.3s forwards;
}
@keyframes clock-slide-left {
  0% {
    transform: translateX(-100px);
    opacity: 0;
  }
  100% {
    transform: translateX(0px);
    opacity: 1;
  }
}
.toolbar ion-button {
  width: 6.4rem;
  height: 1rem;
  font-size: 0.7em;
  margin: 0 0.2em;
  transition: 0.5s;
  --border-radius: 0px;
  --box-shadow: none;
}
.toolbar-mb {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  bottom: 0;
  z-index: 99;
  background: #002bd553;
  height: 4em;
}
.toolbar-mb ion-button{
  width: 8.5rem;
  font-size: 0.9em;
  margin: 0 0.5em;
  transition: 0.5s;
}
.active {
  --background: linear-gradient(135deg, #e56f00 0%, #c35e00 100%);
  color: #fff;
  font-weight: 600;
}
.inactive {

  --background: #002bd553;
  color: #fff;
}
.powered-by {
  color: #fff; 
  background: #222; 
  padding: 0.6em;
  width: auto;
  height: auto;
  font-size: 1em;
  display: flex;
  align-items: center;
}
/* Forecast */
#forecast-wrapper {
  height: 85vh;
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
  color: #e56f00;
  font-weight: 600;
}
.forecast-avg-temp {
  font-size: 2em;
}
.forecast-high-temp, .forecast-low-temp  {
  font-size: 0.7em;
  display: inline-block;
  height: 0;
}
.f-day {
  height: auto;
  border-radius: 10px;
  padding: 0.5em;
  margin: 0 0.5em;
  background: #fff;
  opacity: 0;
  animation: slide-forecast-day-up 0.5s ease 1s forwards
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
    transform: translateY(300px);
    opacity: 0;
  }
  100% {
    transform: translateY(150px);
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
  width: 100%;
  height: 100vh;
}
.picdet {
  
  background: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #ffffff), 
    url('../public//images/detroit_weather.jpeg');
}
.picsa {
  background-image: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #ffffff), url('../public/images/southafrica_weather.jpeg');
}
.pictoyko {
  /* animation: moveBackground 10s linear forwards; */
  background-image: 
    linear-gradient(
      165deg,
    #00000000 40%, 
    #ffffff), url('../public/images/tokyo_weather.jpeg');
}
@media (max-width: 600px) {
  .picdet {
    background: 
      linear-gradient(
        165deg,
      #00000000 40%, 
      #000000), 
      url('../public/images/det_mb.png');
    background-position-x: -100px;
  }
  .picsa {
    background: 
      linear-gradient(
        165deg,
      #00000000 40%, 
      #000000), 
      url('../public/images/sa_mb.png');
  }
  .pictoyko {
    background: 
      linear-gradient(
        165deg,
      #00000000 40%, 
      #000000), 
      url('../public/images/tokyo_mb.png');
    background-position-x: -100px;
  }
}
</style>
