<template>
  <q-page class="q-pt-md q-px-md flex column" :class="bgClass">
        <q-input
          class="q-mb-xl"
           v-model="search"
           placeholder="Search"
           dark
           borderless
           @keyup.enter="getWeatherBySearch"
           >
        <template v-slot:before>
          <q-icon name="my_location" @click="getLocation" />
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" 
          @click="getWeatherBySearch"
          />
        </template>
      </q-input>

      <template v-if="weatherData">
      <div class="col text-center text-white">
        <div class="text-h3 q-my-lg text-weight-thin
">{{ weatherData.name }}</div>
        <div class="text-h5 text-weight-thin  q-my-lg">{{ weatherData.weather[0].main }}</div>
        <div class="text-h1  text-weight-thin">{{ Math.round(weatherData.main.temp) }}°C</div>
      </div>

       <div class="col text-center text-white">
          <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`" alt="">         
       </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-text text-weight-thin flex flex-center">
            Quasar<br>Weather
        </div>
        <q-btn class="col" flat @click="getLocation">
         <q-icon
           name="my_location"
           left
           size="2em"
           ></q-icon>
            <div>Find my location</div>
        </q-btn>

      </div>
    </template>

 <div class="skyline"></div>
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiKey: '07e8bb04cea5cb6e0907cbbaa157b942',
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather'

    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show();
      navigator.geolocation.getCurrentPosition(position =>  {
        console.log(position)
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
      })
      
    },
    getWeatherByCoords() {
      this.$axios(`${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`).then(response => {
        this.weatherData = response.data
        this.$q.loading.hide();
      });
    },
    getWeatherBySearch() {
      this.$axios(`${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`).then(response => {
        this.weatherData = response.data
        this.$q.loading.hide();
      });
    }
  },
  computed: {
    bgClass() {
      if(this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night';
        }
        else {
          return'bg-day';
        }
      }
    }
  }
}
</script>

<style lang="scss">
  .q-page {
              background: linear-gradient(to bottom, #2c3e50, #4ca1af);


      &.bg-day {
            background: linear-gradient(to bottom, #369adc, #5b86e5); 

      }
      &.bg-night {
          background: linear-gradient(to bottom, #141e30, #243b55); 
      }
  }
  .skyline{
    flex: 0 0 200px;
    background: url('../assets/silhouette.png') bottom center;
    background-size: contain;
    background-repeat: repeat-x;
    margin: 0 -16px;
  }
</style>