<template>
  <div class="tickets">
      <div class="ticket" v-for="ticket in tickets" :key="ticket.arrival_time">
          <!-- Лого + цена -->
          <div class="ticket__left-side">
            <img src="../assets/airlines.jpeg" class="ticket__left-side-logo" alt="">
            <button class="ticket__left-side-buy" type="button" name="button">Купить <br> за {{getPrice(ticket.price)}}</button>
          </div>
          <!-- Инфо о перелете -->
          <div class="ticket__right-side">
            <div class="ticket__right-side-info departure">
              <h1 class="time">{{ticket.departure_time}}</h1>
              <h3 class="city">{{ticket.origin}}, {{ticket.origin_name}}</h3>
              <p class="date">{{getDate(ticket.departure_date)}}</p>
            </div>
            <div class="ticket__right-side-info arrival">
              <h1 class="time">{{ticket.arrival_time}}</h1>
              <h3 class="city">{{ticket.destination_name}}, {{ticket.destination}}</h3>
              <p class="date">{{getDate(ticket.arrival_date)}}</p>
            </div>
            <div class="transfers">
              <p>{{ticket.stops}} пересад{{getEndTransfers(ticket.stops)}} </p>
              <img src="../assets/plane.svg" alt="">
            </div>
          </div>

        </div>
  </div>
</template>

<script>
export default {
  name: 'Tickets',
  data(){
    return{
      days: [
        'Вс',
        'Пн',
        'Вт',
        'Ср',
        'Чт',
        'Пт',
        'Сб'
      ],
      usd: 71,
      eur: 77,
    }
  },
  props: ['tickets', 'currency'],
  methods:{
    // отображение цены в разной валюте
    getPrice(cur){
      let res
      this.currency == 'rub' ? res = cur + '₽':
      this.currency == 'usd' ? res = Math.ceil(cur / this.usd) + '$' :
      this.currency == 'eur' ? res = Math.ceil(cur / this.eur) + '€': res = cur + '₽'
      return res
    },
    // меняем окончание слова "пересадка" в зависимости от их количества
    getEndTransfers(word){
      let res = 'ка'
      word > 1 ? res = 'ки' : word < 1 ? res = 'ок' : res = 'ка'
      return res
    },
    // меняем формат даты
    getDate(date){
      var allDate = date.split('.')
      // день
      allDate[0] = Number(allDate[0])
      // месяц
      var month = new Date(allDate[1]).toLocaleString('ru', { month: 'long' }).substr(0, 3); // уменьшаем до 3 символов
      // год
      allDate[2] = '20' + allDate[2]
      // день недели
      
      var day = new Date(allDate[2], allDate[1] - 1, allDate[0]).getDay();

      allDate[1] = month
      // возвращаем результат
      return allDate.join(' ') + `, ${this.days[day]}`
    },
  },
  mounted(){
  }
}
</script>

<style scoped lang="scss">
@media (min-width: 840px) {
  @import '../assets/style/settings1024.scss';
}

@media (min-width: 600px) and (max-width: 840px) {
  @import '../assets/style/settings600.scss';
}

@media (max-width: 600px) {
  @import '../assets/style/settings320.scss';
}
</style>
