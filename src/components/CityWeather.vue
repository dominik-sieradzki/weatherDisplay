<script setup>

//import CityForecast from "@/components/CityForecast.vue";
</script>

<template>
  <div class="city-name h1">{{weatherData.city.name}}</div>
  <div class="container">
    <div class="row weather-header">
      <div class="col">Time</div>
      <div class="col">Temperature</div>
      <div class="col">Weather</div>
      <div class="w-100"></div>
      <template v-for="(weatherPayload) in weatherData.list" :key="weatherPayload.time">
        <div class="col">{{ weatherPayload.dt_txt }}</div>
        <div class="col">{{ weatherPayload.main.temp }}°C</div>
        <div class="col weather-description">{{ weatherPayload.weather[0].description}} <img alt="" :src="getWeatherIcon(weatherPayload.weather[0].main)" class="weather-icon" /></div>
        <div class="w-100"></div>
      </template>
    </div>
  </div>
  <button @click="removeCity(weatherData.id)">Remove City</button>

</template>

<script>
//import WeatherIcon from "./WeatherIcon.vue";

export default {
  props: {
    weatherData: {
      type: Object,
      required: true,
    }
  },
  methods: {
    removeCity(id) {
      this.$emit("remove-city", id);
    },
    getWeatherIcon(icon) {
      // Replace 'icons/' with the folder path where your weather icons are stored
      console.log(`../assets/${icon}.png`);
      return  require(`../assets/${icon}.png`);
    },
  },
};
</script>
<style scoped>
.container {
  margin-top: 20px;
  max-height: 80vh; /* Set the maximum height of the container to 80% of the viewport height */
  overflow: auto; /* Add scrollbars when content exceeds the container height */
}

.city-name {
  margin-bottom: 10px;
}

.weather-header {
  font-weight: bold;
  border-bottom: 1px solid #ccc;
  margin-bottom: 10px;
}

.row {
  display: flex;
  align-items: center;
  border: 1px solid #ccc;
  margin-bottom: 5px;
}

.col {
  flex: 1;
  padding: 5px;
  border: 1px solid #ccc;
}


.weather-icon {
  width: 30px;
  height: 30px;
}
</style>