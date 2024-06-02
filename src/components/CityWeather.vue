<template>
  <v-container>
    <v-row>
      <v-col>
        <v-card>
          <v-card-title class="text-h5">Next 5 Hours</v-card-title>
          <v-row class="pa-3" v-if="weatherHourly.length">
            <v-col class="text-center border-e-thin" v-for="(weather, index) in weatherHourly" :key="`hour-${index}`">
              <HourTile :weather="weather"/>
            </v-col>
          </v-row>
          <v-card-text v-else class="text-center text-h4 text-grey-darken-2">No data available</v-card-text>
        </v-card>
      </v-col>
    </v-row>

  </v-container>

  <v-container>
    <v-row>
      <v-col>
        <v-card>
          <v-card-title class="text-h5">Next 5 Days</v-card-title>
          <v-list class="pa-3" v-if="weatherDaily.length">
            <v-list-item class="border-b-thin" v-for="(weather, index) in weatherDaily" :key="`day-${index}`">
              <DailyTile :weather="weather"/>
            </v-list-item>
          </v-list>
          <v-card-text v-else class="text-center text-h4 text-grey-darken-2">No data available</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>

  <v-card-actions>
    <v-card-text class="text-left">{{ formattedLastUpdated }}</v-card-text>
    <v-spacer></v-spacer>
    <v-btn class="justify-end" @click="refresh">Refresh</v-btn>
</v-card-actions>
</template>

<script>
import axios from "axios";
import HourTile from "@/components/HourTile.vue";
import DailyTile from "@/components/DailyTile.vue";

export default {
  name: 'CityWeather',
  components: {
    DailyTile,
    HourTile
  },
  props: {
    lat: Number,
    lon: Number,
    display: Boolean
  },
  data() {
    return {
      weatherHourly: [],
      weatherDaily: [],
      lastUpdated: null
    }
  },
  mounted() {
    if (this.display) {
      this.getWeather();
    }
  },
  computed: {
    loadData() {
      return this.weatherHourly.length === 0 || this.weatherDaily.length === 0 || this.lastUpdated == null;
    },
    formattedLastUpdated() {
      return this.lastUpdated ? this.lastUpdated.toLocaleString() : '';
    }
  },
  methods: {
    async getWeather() {
      try {
        if (this.lat == null || this.lon == null) {
          return;
        }
        const data = await this.fetchWeather();
        this.weatherHourly = this.sliceArray(data.hourly);
        this.weatherDaily = this.sliceArray(data.daily);
        this.lastUpdated = new Date();
      } catch (error) {
        console.error(error);
      }
    },
    sliceArray(arr) {
      if (arr == null || arr.length === 0) {
        return [];
      }
      return arr.slice(0, 5);
    },
    async fetchWeather() {
      try {
        const excludes = 'current,minutely,alerts';
        const response = await axios.get(
          'https://api.openweathermap.org/data/2.5/onecall',
          { params: {
              appid: import.meta.env.VITE_API_KEY,
              lat: this.lat,
              lon: this.lon,
              exclude: excludes,
              units: 'metric'
            }
          });
        return response.data;
      } catch (error) {
        console.error(error);
        return { hourly: [], daily: [] };
      }
    },
    refresh() {
      this.getWeather();
    }
  },
  watch: {
    display(newValue) {
      if (newValue && this.loadData) {
        this.getWeather();
      }
    }
  }
}
</script>

<style scoped lang="scss">
.border-e-thin {
  &:last-child {
    border-right: none !important;
  }
}

.border-b-thin {
  &:last-child {
    border-bottom: none !important;
  }
}
</style>
