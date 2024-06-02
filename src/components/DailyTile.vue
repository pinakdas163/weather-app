<template>
  <v-row class="border-b-thin">
    <v-col>
      <v-img height="80" :src="`https://openweathermap.org/img/wn/${icon}.png`" />
    </v-col>
    <v-col>
      <v-card-title class="text-h6">{{ date }}</v-card-title>
      <v-card-text class="text-body-2">{{ weatherText }}</v-card-text>
    </v-col>
    <v-col>
      <v-card-text>
        <span class="font-weight-medium text-orange-darken-4 pa-2"> {{ maxTemp }} </span>
        <span class="font-weight-medium text-blue-darken-3 pa-2"> {{ minTemp }} </span>
      </v-card-text>
    </v-col>
  </v-row>
</template>

<script>
export default {
  name: 'DailyTile',
  props: {
    weather: {
      type: Object,
      required: true
    }
  },
  computed: {
    date() {
      if (this.weather.dt) {
        const date = new Date(this.weather.dt * 1000);
        const day = date.getDate();
        const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'];
        const month = months[date.getMonth()];
        const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday']
        const dayOfWeek = days[date.getDay()];
        return `${dayOfWeek}, ${month} ${day}`;
      }
      return '';
    },
    icon() {
      return this.weather.weather && this.weather.weather.length > 0 ? this.weather.weather[0].icon : '';
    },
    maxTemp() {
      return this.weather.temp && this.weather.temp.max ? Math.round(this.weather.temp.max) + '°C' : '';
    },
    minTemp() {
      return this.weather.temp && this.weather.temp.min ? Math.round(this.weather.temp.min) + '°C' : '';
    },
    weatherText() {
      return this.weather.weather && this.weather.weather.length > 0 ?
        this.capitalizeFirstLetter(this.weather.weather[0].description) + '.' : '';
    }
  },
  methods: {
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    }
  }
}
</script>
<style scoped lang="sass">

</style>
