
<template>
  <p class="lead">
    Welcome to weather page. Here you can call 3-hourly weather forecasts for the next 48 hours, for any city in the world.
  </p>
  <div class="app container mt-4">
    <div class="controls mb-3">
      <input v-model="selectedCity">
      <button @click="addCity(selectedCity)">Add city</button>
    </div>
        <CityWeather
            v-for="(weatherData) in weatherByCity"
            :key="weatherData.id"
            :city="weatherData.city.name"
            :weatherData="weatherData"
            @remove-city="removeCity(weatherData.id)"
        />
  </div>

</template>
<script>

//import WeatherIcon from './components/WeatherIcon.vue'
import CityWeather from "@/components/CityWeather.vue";
import axios from "axios";
import 'bootstrap';
import 'bootstrap-vue';
import Big from "big.js";
export default {
  name: 'App',
  components: {
    CityWeather,
    //WeatherIcon
  },
  data() {
    return {
      defaultCity: "Amsterdam",
      selectedCity: "Amsterdam",
      weatherByCity: [],
    };
  },
  beforeMount() {
    this.addCity(this.defaultCity); // Display the default city on startup
  },
  methods: {
    addCity(city) {
      const api = "ad01ed8cf15282f7fc53d5bfffb0867c";
      let lat = Big(0);
      let lon = Big(0);

      axios
          .get(`https://api.openweathermap.org/geo/1.0/direct?q=${city}&limit=1&appid=${api}`)
          .then((response) => {
            console.log(response);
            lat = Big(response.data[0].lat);
            lon = Big(response.data[0].lon);
          })
          .catch((error) => {
            console.error(error);
          })
          .finally(() => {
            axios
                .get(`https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&appid=${api}&units=metric`)
                .then((response) => {
                  console.log(response);
                  response.data.id = this.weatherByCity.length;
                  this.weatherByCity.push(response.data);
                })
                .catch((error) => {
                  console.error(error);
                });
          });

    },
    removeCity: function(city) {
      const index = this.weatherByCity.findIndex((data) => data.id === city);
      if (index !== -1) {
        this.weatherByCity.splice(index, 1);
      }
      console.log(this.weatherByCity);
    },
  },
};
</script>

<style>
@import 'bootstrap';
@import 'bootstrap-vue';
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.lead{
  font-family: "Comic Sans MS";
  font-size: 40px;
}
</style>
