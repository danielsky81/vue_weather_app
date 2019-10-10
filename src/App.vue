<template>
  <div id="app">
    <b-container fluid>
      <b-row>
        <b-col sm="12" md="12" lg="4">
          <div ref="top">
            <b-card
              title="DUBLIN"
              :img-src="dublin_current.symbol"
              img-alt="Current weather status"
              img-top
              tag="article"
              style="max-width: 20rem;"
              class="mb-2">
              <b-card-text>
                The current temperature is <strong>{{ dublin_current.temp }}</strong> with <strong>{{ dublin_current.forecast }}</strong>
              </b-card-text>
              <b-button v-b-toggle.accordion-dublin variant="outline-info" @click="fetchForecast('7778677'); scrollMeTo('dublin')">5 Days Forecast</b-button>
            </b-card>
          </div>
        </b-col>
        <b-col sm="12" md="6" lg="4">
          <div>
            <b-card
              title="CORK"
              :img-src="cork_current.symbol"
              img-alt="Current weather status"
              img-top
              tag="article"
              style="max-width: 20rem;"
              class="mb-2">
              <b-card-text>
                The current temperature is <strong>{{ cork_current.temp }}</strong> with <strong>{{ cork_current.forecast }}</strong>
              </b-card-text>
              <b-button v-b-toggle.accordion-cork variant="outline-info" @click="fetchForecast('2965140'); scrollMeTo('cork')">5 Days Forecast</b-button>
            </b-card>
          </div>
        </b-col>
        <b-col sm="12" md="12" lg="4">
          <div>
            <b-card
              title="GALWAY"
              :img-src="galway_current.symbol"
              img-alt="Current weather status"
              img-top
              tag="article"
              style="max-width: 20rem;"
              class="mb-2">
              <b-card-text>
                The current temperature is <strong>{{ galway_current.temp }}</strong> with <strong>{{ galway_current.forecast }}</strong>
              </b-card-text>
              <b-button v-b-toggle.accordion-galway variant="outline-info" @click="fetchForecast('2964179'); scrollMeTo('galway')">5 Days Forecast</b-button>
            </b-card>
          </div>
        </b-col>
      </b-row>
      <b-row>
        <b-col sm="12" ref="dublin">
          <b-collapse id="accordion-dublin" accordion="my-accordion" class="mt-2">
            <h2 class="mainTitle">Dublin Extended Forecast</h2>
            <b-card :key="item.id" v-for="item in dublin">
              <p>{{ item.day }} a</p>
              <p>t <strong>{{ item.hour }}:00</strong></p>
              <p><img class="img-fluid" :src="item.symbol"></img></p>
              <h3>{{ item.temp }}</h3>
            </b-card>
          </b-collapse>
        </b-col>
        <b-col sm="12" ref="cork">
          <b-collapse id="accordion-cork" accordion="my-accordion" class="mt-2">
            <h2 class="mainTitle">Cork Extended Forecast</h2>
            <b-card :key="item.id" v-for="item in cork">
              <p>{{ item.day }} a</p>
              <p>t <strong>{{ item.hour }}:00</strong></p>
              <p><img class="img-fluid" :src="item.symbol"></img></p>
              <h3>{{ item.temp }}</h3>
            </b-card>
          </b-collapse>
        </b-col>
        <b-col sm="12" ref="galway">
          <b-collapse id="accordion-galway" accordion="my-accordion" class="mt-2">
            <h2 class="mainTitle">Galway Extended Forecast</h2>
            <b-card :key="item.id" v-for="item in galway">
              <p>{{ item.day }} a</p>
              <p>t <strong>{{ item.hour }}:00</strong></p>
              <p><img class="img-fluid" :src="item.symbol"></img></p>
              <h3>{{ item.temp }}</h3>
            </b-card>
          </b-collapse>
        </b-col>
      </b-row>
      <div id="scrollTop">
        <b-button variant="outline-primary" @click="scrollMeTo('top')">Top</b-button>
      </div>
    </b-container>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
        return {
          dublin_current: '',
          cork_current: '',
          galway_current: '',
          dublin: '',
          cork: '',
          galway: ''
        };
      },
      created() {
        this.dublin_current = this.fetchWeather(7778677);
        this.cork_current = this.fetchWeather(2965140);
        this.galway_current = this.fetchWeather(2964179);
      },
      methods: {
        fetchWeather(city) {
          let url = `https://api.openweathermap.org/data/2.5/weather?id=${city}&units=metric&APPID=74ecf887ea2ee80ab6586f67dfe5ee24`
          this.$http.get(url)
            .then(response => {
              let data = response.data;
              let output = {
                temp: `${Math.round(data.main.temp).toString()} \u00B0C`,
                forecast: data.weather[0].description,
                symbol: `http://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`
              }
              if (city == '7778677') {
                this.dublin_current = output;
              } else if (city == '2965140') {
                this.cork_current = output;
              } else if (city == '2964179') {
                this.galway_current = output;
              }
            })
            .then(data => {
              let output = {
                temp: `${Math.round(data.main.temp).toString()} \u00B0C`,
                forecast: data.weather[0].description,
                symbol: `http://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`
              }
            });
        },
        fetchForecast(city) {
          let url = `https://api.openweathermap.org/data/2.5/forecast?id=${city}&units=metric&APPID=74ecf887ea2ee80ab6586f67dfe5ee24`
          this.$http.get(url)
            .then(response => {
              return response.json();
            })
            .then(data => {
              const months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
                let selection = [];
                let getSelection = cityId => {
                  for (let key in data.list) {
                  let output = {
                    day: `${new Date(data.list[key].dt*1000).getUTCDate()} - ${months[new Date((data.list[2].dt)*1000).getUTCMonth()]}`,
                    hour: new Date(data.list[key].dt*1000).getUTCHours(),
                    temp: `${Math.round(data.list[key].main.temp).toString()} \u00B0C`,
                    forecast: data.list[key].weather[0].description,
                    symbol: `http://openweathermap.org/img/wn/${data.list[key].weather[0].icon}@2x.png`
                  }
                    selection.push(output);
                  }
                }
              if (city == '7778677') {
                getSelection(city);
                this.dublin = selection;
              } else if (city == '2965140') {
                getSelection(city);
                this.cork = selection;
              } else if (city == '2964179') {
                getSelection(city);
                this.galway = selection;
              }
            });
        },
        scrollMeTo(refName) {
          let element = this.$refs[refName];
          let top = element.offsetTop;
          if (window.innerWidth < 992) {
            window.scrollTo(0, top);
          } else if (refName == 'top') {
            window.scrollTo(0, top);
          }
        }
      }
    }
</script>

<style lang="scss">
@import 'node_modules/bootstrap/scss/bootstrap';
@import 'node_modules/bootstrap-vue/src/index.scss';
</style>
