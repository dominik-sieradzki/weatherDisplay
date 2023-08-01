<script setup>

</script>

<template>
  <div class="city-name h-75">Default cities. Those will persist when you close the webpage.</div>
  <div class="container">
    <div class="row weather-header">
    <template v-for="city in defaultCities" :key="city">
      <div class="col">{{ city }}
        <button @click="removeDefaultCity(city)">Remove</button>
      </div>
      <div class="w-100"></div>
    </template>
    </div>
  </div>
  <br>
</template>
<script>

export default {
  props: {
    defaultCities: {
      type: Array,
      required: true,
    },
  },
  methods: {
    removeDefaultCity(city) {
      // Emit an event to notify the parent component to remove the city
      this.$emit("remove-default-city", city);
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

</style>