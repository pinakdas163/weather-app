<template>
  <v-app>
    <v-app-bar app color="primary">
      <v-toolbar-title class="white--text">Simple Weather App</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn @click="showAutocomplete = !showAutocomplete">
        <v-icon>mdi-magnify</v-icon>
      </v-btn>
      <v-text-field
        placeholder="Search city"
        v-model="searchedCity"
        @keyup.enter="searchCity"
        @blur="hideSearchBar"
        v-if="showAutocomplete"
        :error="cityNotFoundError"
      >
      </v-text-field>
    </v-app-bar>
    <v-main>
      <v-container>
        <v-card>
          <v-tabs
            v-model="selectedCity"
            bg-color="white"
          >
            <v-tab
              v-for="coord in cityCoords"
              :key="coord.city"
              :value="coord.city"
            >
              {{ coord.city }}
            </v-tab>
          </v-tabs>

          <v-card-text>
            <v-tabs-window v-model="selectedCity">
              <v-tabs-window-item v-for="coord in cityCoords"
                                  :key="`city-${coord.city}`" :value="coord.city">
                <CityWeather :lat="coord.lat" :lon="coord.lon" :display="selectedCity === coord.city"/>
              </v-tabs-window-item>
            </v-tabs-window>
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import CityWeather from "@/components/CityWeather.vue";
import cities from './assets/cities.js';
import axios from "axios";

export default {
  name: 'App',
  components: {
    HelloWorld,
    CityWeather
  },
  data() {
    return {
      cityCoords: [],
      selectedCity: 'Rio de Janeiro',
      showAutocomplete: false,
      searchedCity: '',
      cityNotFoundError: false
    }
  },
  created() {
    const defaultCities = ['Rio de Janeiro', 'Beijing', 'Los Angeles'];
    defaultCities.forEach(city => {
      this.getCityCoord(city).then(data => {
        if (data) this.cityCoords.push(data);
      });
    });
  },
  methods: {
    searchCity() {
      const city = this.searchedCity.trim().toLowerCase();
      const searchedCityCoords = cities.hasOwnProperty(city) ? cities[city] : null;
      if (searchedCityCoords) {
        this.cityFound(searchedCityCoords);
      } else {
        this.getCityCoord(city).then(searchedCityCoords => {
          if (searchedCityCoords) {
            this.cityFound(searchedCityCoords);
          } else {
            this.cityNotFoundError = true;
          }
        });
      }
    },
    async getCityCoord(city) {
      try {
        const response = await axios.get(
          'https://api.openweathermap.org/data/2.5/weather',
          { params: {
              appid: import.meta.env.VITE_API_KEY,
              q: city
            }
          });
        const data = await response.data.coord;
        return { city, lat: data.lat, lon: data.lon };
      } catch (error) {
        console.error(error);
      }
    },
    addOrReplaceCity(cityCoord) {
      if (this.cityCoords.length <= 3) {
        this.cityCoords.push(cityCoord);
      } else {
        this.cityCoords.pop();
        this.cityCoords.push(cityCoord);
      }
    },
    cityFound(cityCoord) {
      this.addOrReplaceCity(cityCoord);
      this.showAutocomplete = false;
      this.searchedCity = '';
      this.cityNotFoundError = false;
      this.$nextTick(() => {
        this.selectedCity = cityCoord.city;
      });
    },
    hideSearchBar() {
      this.showAutocomplete = false;
      this.cityNotFoundError = false;
    }
  }
}
</script>
