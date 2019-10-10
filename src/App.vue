<template>
  <div id="app">
    <b-container fluid>
        <b-row>
            <b-col>
              <b-row>
                <b-col>
                  <!-- <img class="img-fluid" :src="dublin[symbol]"></img> -->
                  <b-button v-b-toggle.collapse-dublin variant="outline-info" @click="fetchForecast('7778677')">Dublin</b-button>
                  <b-collapse id="collapse-dublin" class="mt-2">
                    <b-card>
                      <ul>
                        <li :key="item.id" v-for="item in dublin">
                          <p>{{ item.day }} {{ item.hour }} {{ item.temp }}</p>
                          <p><img class="img-fluid" :src="item.symbol"></img></p>
                        </li>
                      </ul>
                    </b-card>
                  </b-collapse>
                </b-col>
                <b-col>
                  <b-button v-b-toggle.collapse-cork variant="outline-info" @click="fetchForecast('2965140')">Cork</b-button>
                  <b-collapse id="collapse-cork" class="mt-2">
                    <b-card>
                      <ul>
                        <li :key="item.id" v-for="item in cork">
                          <p>{{ item.day }} {{ item.hour }} {{ item.temp }}</p>
                          <p><img class="img-fluid" :src="item.symbol"></img></p>
                        </li>
                      </ul>
                    </b-card>
                  </b-collapse>
                </b-col>
                <b-col>
                  <b-button v-b-toggle.collapse-galway variant="outline-info" @click="fetchForecast('2964179')">Galway</b-button>
                  <b-collapse id="collapse-galway" class="mt-2">
                    <b-card>
                      <ul>
                        <li :key="item.id" v-for="item in galway">
                          <p>{{ item.day }} {{ item.hour }} {{ item.temp }}</p>
                          <p><img class="img-fluid" :src="item.symbol"></img></p>
                        </li>
                      </ul>
                    </b-card>
                  </b-collapse>
                </b-col>
              </b-row>
            </b-col>
        </b-row>
    </b-container>
  </div>
</template>

<script>
export default {
  name: 'app',
  data() {
        return {
          fields: ['day', 'hour', 'temp', 'forecast'],
          dublin: '',
          cork: '',
          galway: ''
        };
      },
      methods: {
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
        }
      }
    }
</script>

<style lang="scss">
@import 'node_modules/bootstrap/scss/bootstrap';
@import 'node_modules/bootstrap-vue/src/index.scss';
</style>
