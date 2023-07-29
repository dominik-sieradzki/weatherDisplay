<script setup>

</script>

<template>
  <div class="weather-icon">
    <img :src="getWeatherIconUrl" :alt="icon" />
  </div>
</template>

<script>
export default {
  props: {
    icon: {
      type: String,
      required: true,
    },
  },
  computed: {
    getWeatherIconUrl() {
      // Replace 'icons/' with the folder path where your weather icons are stored
      return `icons/${this.icon}.svg`;
    },
  },
};
</script>

<style>
/* Add your CSS styling for WeatherIcon component here */
</style>


<style scoped>

</style>