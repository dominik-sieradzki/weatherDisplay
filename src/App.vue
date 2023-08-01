
<template>
  <p class="lead">
    Welcome to weather page. Here you can call 3-hourly weather forecasts for the next 48 hours, for any city in the world.
  </p>
  <div class="app container mt-4">
    <div class="controls mb-3">
      <input v-model="defCityInput">
      <button @click="addDefaultCity(defCityInput)">Add default city</button>
      <br>
      <DefaultCitiesList :defaultCities="defaultCities" @remove-default-city="removeDefaultCity" />
      <input v-model="selectedCity">
      <button @click="addCity(selectedCity)">Add city</button>
      <br>
      <p>{{errorTooltip}}</p>
    </div>
        <CityWeather
            v-for="(weatherData) in weatherByCity"
            :key="weatherData.id"
            :city="weatherData.city.name"
            :weatherData="weatherData"
            @remove-city="removeCity(weatherData.id, weatherData.city.name)"
        />
  </div>

</template>
<script>

import CityWeather from "@/components/CityWeather.vue";
import axios from "axios";
import 'bootstrap';
import 'bootstrap-vue';
import Big from "big.js";
import VueCookies from "vue-cookies";
import DefaultCitiesList from "@/components/DefaultCitiesList.vue";

export default {
  name: 'App',
  components: {
    DefaultCitiesList,
    CityWeather,
    //WeatherIcon
  },
  data() {
    return {
      rmCityInput: "",
      defCityInput: "Amsterdam",//we want defaultCity to only hold valid city at all times
      defaultCities: ["Amsterdam"],
      selectedCity: "Amsterdam",
      weatherByCity: [],
      api: "ad01ed8cf15282f7fc53d5bfffb0867c",
      errorTooltip: ""
    };
  },
  beforeMount() {
    const defaultCitiesFromCookie = VueCookies.get("defaultCities");
    console.log("hoohooo " + defaultCitiesFromCookie);
    if (defaultCitiesFromCookie != null) {

      this.defaultCities = defaultCitiesFromCookie;
      this.defaultCities.forEach((city) => {
        this.addCity(city); // Add each default city to the weatherByCity array
      });
    }
    else{
      console.log("null cookie");
      this.defaultCities = ["Amsterdam"];
      this.addCity("Amsterdam");
      this.storeDefaultCitiesInCookie();
    }
  },
  methods: {
    addDefaultCity(city){
      axios
          .get(`https://api.openweathermap.org/geo/1.0/direct?q=${city}&limit=1&appid=${this.api}`)
          .then((response) => {
            console.log(response.data[0].name);
            console.log(city);
            if(response.data[0].name !== city){
              this.errorTooltip = "Cannot find this exact city. Please check spelling"
            }
            else if(!this.checkDupe(response.data[0].name)){
              this.errorTooltip = "";
              this.defaultCities.push(city);
              this.storeDefaultCitiesInCookie();
              this.addCity(city);
            }
            else{
              this.errorTooltip = "City already exists"
            }
          })
          .catch((error) => {
            this.errorTooltip = "Something went wrong. Please check your entry's spelling"
            console.error(error);
          })
    },
    removeDefaultCity(cityName){
      if (this.defaultCities.length <= 1){
        this.errorTooltip = "cannot remove the last default city";
        return;
      }
      const index = this.defaultCities.findIndex((data) => data === cityName);
      if (index !== -1) {
        this.defaultCities.splice(index, 1);
      }
      this.storeDefaultCitiesInCookie();
    },
    checkDupe(cityName){//checks for duplicate of a city by name
      return this.weatherByCity.some((cityData) => {
        return cityData.city.name === cityName;
      });
    },
    checkDefaultCity(cityName){//checks if its in default cities
      return this.defaultCities.some((name) => {
        return name === cityName;
      });

    },
    addCity(city) {
      let lat = Big(0);
      let lon = Big(0);

      axios
          .get(`https://api.openweathermap.org/geo/1.0/direct?q=${city}&limit=1&appid=${this.api}`)
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
                .get(`https://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&appid=${this.api}&units=metric`)
                .then((response) => {
                  console.log(response);
                  if(!this.checkDupe(response.data.city.name)){
                    this.errorTooltip = "";
                    response.data.id = this.weatherByCity.length;//with this we can't use unshift() and so on on weatherByCity array without messing up the id. Todo: find a way to get current index of every city so we dont have to rely on custom id values. Or just put default city in separate array and display it before the rest
                    this.weatherByCity.push(response.data);
                  }
                  else this.errorTooltip = "City already exists"
                })
                .catch((error) => {
                  console.error(error);
                });
          });

    },
    storeDefaultCitiesInCookie() {
      VueCookies.set("defaultCities", this.defaultCities, 365); // Store for 365 days
    },
    removeCity(city,name) {
      console.log(name);
      if(this.checkDefaultCity(name)) {
        this.errorTooltip = "Cannot remove default city.";
        return;
      }
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
