<script lang="ts">
import axios from "axios";

const weatherToken = import.meta.env.VITE_WEATHER_TOKEN;

export default {
  data() {
    return {
      cityInput: "",
      city: "",
      temperature: "",
      weather: "",
      humidity: "",
      iconCode: "",
      iconUrl: "",
      errorMessage: "",
    };
  },

  methods: {
    getWeather() {
      if (!weatherToken) {
        this.errorMessage = "La clé API OpenWeatherMap est vide...";
        return;
      }
      axios
        .get(
          `http://api.openweathermap.org/data/2.5/weather?q=${this.cityInput}&units=metric&APPID=${weatherToken}&lang=fr`
        )
        .then((response) => {
          this.city = this.cityInput;
          this.temperature = response.data.main.temp;
          this.weather = response.data.weather[0].description;
          this.humidity = response.data.main.humidity;
          this.iconCode = response.data.weather[0].icon;
          this.iconUrl = `http://openweathermap.org/img/w/${this.iconCode}.png`;
          this.errorMessage = "";
        })
        .catch((error) => {
          if (error.response.status === 401) {
            this.errorMessage = "Votre clé API semble être invalide...";
          } else {
            console.log(error);
            this.city = "";
            this.errorMessage = `Impossible de trouver la ville "${this.cityInput}"`;
          }
        });
    },
  },
};
</script>

<template>
  <div class="bg-gray-100 h-screen">
    <div class="container mx-auto px-4 py-8 w-2/6">
      <h1 class="text-3xl font-bold text-center mb-8">Météo</h1>
      <div class="flex items-center mb-8">
        <input
          class="border rounded-l-md px-4 py-2 w-full"
          type="text"
          placeholder="Entrez le nom de la ville"
          v-model="cityInput"
          @keyup.enter="getWeather"
        />
        <button
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold rounded-r-md px-4 py-2"
          @click="getWeather"
        >
          Rechercher
        </button>
      </div>
      <div class="bg-white rounded-lg p-8" v-if="city">
        <div class="flex items-center mb-4">
          <img :src="iconUrl" class="w-20 h-20" />
          <div class="ml-4" v-if="city">
            <h2 class="text-2xl font-bold">{{ city }}</h2>
            <p class="text-lg">{{ weather }}</p>
          </div>
        </div>
        <div class="flex items-center">
          <div class="mr-8">
            <p class="text-lg font-bold">{{ temperature }}°C</p>
            <p class="text-sm">Température</p>
          </div>
          <div class="mr-8">
            <p class="text-lg font-bold">{{ humidity }}%</p>
            <p class="text-sm">Humidité</p>
          </div>
        </div>
      </div>
      <div v-if="errorMessage" class="mt-8 text-red-500">
        {{ errorMessage }}
      </div>
    </div>
  </div>
</template>

<style></style>
