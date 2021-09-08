<template>
  <q-page class="flex column " :class="bgClass">
        <div class="col q-pt-lg q-px-md">
          <q-input  v-model="search" placeholder="Search" dark borderless @keyup.enter="getWeatherbySearch">
        <template v-slot:before>
          <q-icon name="my_location" @click="getLocation"/>
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherbySearch"/>
        </template>
      </q-input>
        </div>
        <template v-if="weatherData">
          <div class="col text-white text-center">
          <div class="text-h4 text-weight-light">
              {{ weatherData.name }}
          </div>
          <div class="text-h6 text-weight-light">
             {{ weatherData.weather[0].main }}
          </div>
          <div class="text-h1 text-weight-thin q-my-lg relative-position">
              <span>{{ weatherData.main.temp }}</span>
              <span class="text-h4 relative-position degree">&deg;C</span>
          </div>
        </div>

        <div class="col text-center">
            <img :src="`https://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="">
        </div>
        </template>

      <template v-else>
        <div class="col column text-center text-white">
          <div class="col text-h2 text-weight-thin">
            Weather Detection<br>App
          </div>
          <q-btn class="col" flat @click="getLocation">
            <q-icon left size="3em" name="my_location" />
              <div>Find my location</div>
          </q-btn>
        </div>
      </template>

  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData:  null,
      lat: null,
      lon:null,
      url: "https://api.openweathermap.org/data/2.5/weather",
      apiKey: "73de1ba42fdc64c9e202ae41a19be27c"
    }
  },
  computed: {
      bgClass() {
        if(this.weatherData) {
          if(this.weatherData.weather[0].icon.endsWith('n')) {
            return 'bg-night'
          }
          else {
            return 'bg-day'
          }
        }
      }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()
      navigator.geolocation.getCurrentPosition(
        position => {
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
        }
      )
    },
    getWeatherByCoords() {
      this.$q.loading.show()
      this.$axios(`${this.url}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`)
      .then(response => {
        console.log(response.data)
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
    getWeatherbySearch() {
      this.$q.loading.show()
      this.$axios(`${this.url}?q=${this.search}&appid=${this.apiKey}&units=metric`)
      .then(response => {
        console.log(response.data)
        this.weatherData = response.data
         this.$q.loading.hide()
      
      })
    }
  }
}
</script>

<style lang="sass">
  .q-page
    background: linear-gradient(to bottom, #136a8a, #267871)
    &.bg-night
      background: linear-gradient(to bottom, #232526, #414345)
    &.bg-day
      background: linear-gradient(to bottom, #00b4db, #0083b0)
  .degree
    top: -44px
</style>
