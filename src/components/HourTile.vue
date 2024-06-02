<template>
  <v-card-text class="font-weight-bold">{{ temp }}</v-card-text>
  <v-card-text class="text-blue-accent-1">{{ humidity }}</v-card-text>
  <v-img height="50" :src="`https://openweathermap.org/img/wn/${icon}.png`" />
  <v-list-item-title>{{ time }}</v-list-item-title>
</template>

<script>
export default {
  name: 'HourTile',
  props: {
    weather: {
      type: Object,
      required: true
    }
  },
  computed: {
    time() {
      return this.weather.dt ? new Date(this.weather.dt * 1000).toLocaleTimeString(undefined, { hour12: true, hour: 'numeric', minute: '2-digit' }) : '';
    },
    icon() {
      return this.weather.weather && this.weather.weather.length > 0 ? this.weather.weather[0].icon : '';
    },
    temp() {
      return this.weather.temp ? this.weather.temp + '°C' : '';
    },
    humidity() {
      return this.weather.humidity ? this.weather.humidity + '%' : '';
    }
  }
}
</script>

<style scoped lang="sass">

</style>
