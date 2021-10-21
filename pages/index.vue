<template>
  <v-container class="grey lighten-5" >
    <v-row >

      <v-col cols="12">
        <v-flex lg12>
          <v-form @submit.prevent="$fetch">
            <v-text-field v-model="city" label="Regular" solo></v-text-field>
          </v-form>     
        </v-flex>
      </v-col>

      <v-col cols="6" md="6">        
        <v-card
          outlined
          tile
          height="100%">
            <v-flex>
              <v-card-text>
                <v-layout justify-center>
                  <v-flex align-self-start class="text-center">
                    <h1>{{weather.name}}</h1>
                    <p>
                      {{weather.weather[0].description}}
                    </p>     
                    <h3>Temperatura</h3>
                    <h3>{{temp(weather.main.temp)}}º</h3>                       
                    <img :src="icon(weather.weather[0].icon)" height="70" width="70">                
                  </v-flex>
                </v-layout>
                <v-layout v-if="days.alerts">
                  <v-flex>
                    <h4 class="text-center">{{days.alerts[0].event}}</h4>
                    <p>{{days.alerts[0].description}}</p>
                    <p>Start: {{getDate(days.alerts[0].start)}}</p>
                    <p>End: {{getDate(days.alerts[0].end)}}</p>
                  </v-flex>                  
                </v-layout>
              </v-card-text>
          </v-flex>
        </v-card>       
      </v-col>

      <v-col cols="8" md="4">
        <v-card          
          outlined
          tile          
          height="100%"
        >
          <v-card-text>
            <v-layout justify-center>
              <v-flex align-self-start>
                <v-list
                  subheader
                  two-line
                >
                  <v-list-item
                    v-for="(day, index) in days.daily"
                    :key="(day.temp.min, index)"
                  >
                    <v-list-item-avatar>
                      <img :src="icon(day.weather[0].icon)">
                    </v-list-item-avatar>
                    
                    <v-list-item-content>
                      <v-list-item-title >{{getWeekday(index)}}</v-list-item-title>
                      <v-list-item-subtitle>{{temp(day.temp.min)}}/{{temp(day.temp.max)}}ºC</v-list-item-subtitle>
                    </v-list-item-content>
                  </v-list-item>
                </v-list>                                        
              </v-flex>
            </v-layout>            
          </v-card-text>        
        </v-card>
      </v-col>
    </v-row>
  </v-container> 
</template>

<script>
  export default {
    data: () => ({
      city: 'Sao paulo',
      weather: {},
      days: {}
    }),
    async fetch(){
      this.weather = await this.$axios
        .$get(
          `https://api.openweathermap.org/data/2.5/weather?q=${
          this.city
          }&units=metric&appid=b1180f5d0b40919286fcd5324dac4425`
        )
      
      this.days = await this.$axios
        .$get(
          `https://api.openweathermap.org/data/2.5/onecall?lat=${
            this.weather.coord.lat
          }&lon=${
            this.weather.coord.lon
          }&units=metric&appid=b1180f5d0b40919286fcd5324dac4425`
        )
    },
    methods:{
      temp(temp){
        return temp.toFixed(1)
      },
      icon(icon){
        return `https://openweathermap.org/img/wn/${
          icon
          }.png`
      },
      getWeekday(index){
        const current = new Date();
        const weekday = current.getDay();

        if((weekday+index)%7 === 0)
          return "Sunday"
        if((weekday+index)%7 === 1)
          return "Tuesday"
        if((weekday+index)%7 === 2)
          return "Wednesday"
        if((weekday+index)%7 === 3)
          return "Thursday"
        if((weekday+index)%7 === 4)
          return "Friday"
        if((weekday+index)%7 === 5)
          return "Saturday"
        if((weekday+index)%7 === 6)
          return "Monday"
      },
      getDate(date){
        return new Date(date * 1000);
      }
    },
    
  }
</script> 
