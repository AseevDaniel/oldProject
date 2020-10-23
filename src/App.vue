<template>
  <div id="app" :class="{hideScroll: phoneFilter}">
    <div class="logo">
      <img alt="logo" src="./assets/logo.svg">
    </div>
    <div @click="showFilter" :class="{active: phoneFilter, notActive: !phoneFilter}" class="phone-filter">
      <span class="top"></span>
      <span class="bot"></span>
    </div>
    <div class="content">
      <!-- Настройки билетов -->
      <Settings
        :class="{showSettings: phoneFilter, notShowSettings: !phoneFilter}"
        @changeCurency="changeCurency"
        @changeFilter="changeFilter"
        class="settings" />
      <!-- Список билетов -->
      <Tickets 
        :tickets="tickets"
        :currency="currency"
        class="tickets"/>

    </div>
  </div>
</template>

<script>
import Settings from './components/Settings.vue'
import Tickets from './components/Tickets.vue'
import tickets from './tickets.json'
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Settings,
    Tickets
  },
  data(){
    return{
      allTickets: [],
      tickets: [],
      currency: 'rub',
      phoneFilter: false,
    }
  },
  created(){
    this.getTickets()
  },
  mounted() {
    this.getValute()

  },
  methods:{
    // получаем даннные билетов (необходим запуск json-server'а)
    async getTickets(){
      await axios.get('http://localhost:3000/tickets')
      .then( response => {
        console.log('tickets from local server');
        this.allTickets = response.data
      })
      .catch( error => {
        console.log(error);
        console.log('tickets from file');
        this.allTickets = tickets.tickets
      })
      this.sortTickets()
    },
    // получаем api курса валют
    async getValute(){
      await axios.get('https://www.cbr-xml-daily.ru/daily_json.js')
      .then( response => {
        this.usd = response.data.Valute.USD.Value;
        this.eur = response.data.Valute.EUR.Value;
      })
    },
    // сортировка билетов по цене
    sortTickets(){
      this.allTickets.sort( (a, b) => {
        return a.price - b.price
      })
      this.tickets = this.allTickets
    },
    // изменение валюты
    changeCurency(cur){
      this.currency = cur
    },
    // изменение фильтра
    changeFilter(transfers){
      var newArr = []
      for (var i = 0; i < this.allTickets.length; i++) {
        if(transfers.includes(this.allTickets[i].stops)){
          newArr.push(this.allTickets[i])
        }
      }
      this.tickets = newArr
    },
    
    showFilter(){
      this.phoneFilter = !this.phoneFilter
    }
  },
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;600&display=swap');
@media (min-width: 840px) {
  @import './assets/style/tickets1024.scss';
}

@mixin flexPref {
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
}
*{
  font-family: 'Open Sans', sans-serif;
}
body{
  background-color: #f2f2f2;
  margin: 0;
  /* overflow-x: hidden; */
}
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  width: 820px;
  margin: 0 auto;
  margin-top: 40px;
  .showFilter{
    display: none;
  }
  .logo{
    @include flexPref;
    justify-content: center;
  }
  .content{
    margin-top: 30px;
    @include flexPref;
    justify-content: space-between;
  }
}

@media (min-width: 600px) and (max-width: 840px) {
  @import './assets/style/tickets600.scss';
  #app{
    margin-top: 10px;
    margin: 0 auto;
    width: 100vw;
    .showFilter{
      display: none;
    }
    .content{
      display: block;
      width: 80%;
      margin: 0 auto;
    }
  }
}

@media (max-width: 600px) {
  @import './assets/style/tickets320.scss';
  @import './assets/style/filterMobile.scss';
  #app{
    margin-top: 10px;
    overflow-x: auto;
    margin: 0 auto;
    width: 100vw;
    .showFilter{
      display: block;
    }
    .logo{
      position: absolute;
      left: 10vw;
      top: 0;
    }
    .content{
      display: block;
      width: 90%;
      margin: 0 auto;
    }
  }
  .hideScroll{
    height: 100vh;
  }
}

</style>
