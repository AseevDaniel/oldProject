<template>
  <div class="settings">
    <!-- настройки валюты -->
    <div class="settings__currency">
      <h4>Валюта</h4>
      <button :class="{active: currency == 'rub'}" @click="changeCurency('rub')" class="left" type="button" name="button">RUB</button>
      <button :class="{active: currency == 'usd'}" @click="changeCurency('usd')" class="center" type="button" name="button">USD</button>
      <button :class="{active: currency == 'eur'}" @click="changeCurency('eur')" class="right" type="button" name="button">EUR</button>
    </div>
    <!-- фильтр пересадок -->
    <div class="settings__transfers">
      <h4>Количество пересадок</h4>
      <!-- выбрать все -->
      <div class="checkbox">
        <input @click="checkedAll()" class="transfer" type="checkbox" id="all" ref="all" name="transfer" value="all" >
        <label for="all">Все</label>
      </div>
      <!-- ни одной пересадки -->
      <div class="checkbox">
        <input @change="checkbox()" class="transfer transfer-1" type="checkbox" id="zero" ref="zero" name="transfer" value="zero" v-model="transfers">
        <label for="zero">Без пересадок</label>
        <span @click="only('zero')">только</span>
      </div>
      <!-- 1 -->
      <div class="checkbox">
        <input @change="checkbox()" class="transfer transfer-2" type="checkbox" id="one" ref="one" name="transfer" value="one" v-model="transfers">
        <label for="one">1 пересадка</label>
        <span @click="only('one')">только</span>
      </div>
      <!-- 2 -->
      <div class="checkbox">
        <input @change="checkbox()" class="transfer transfer-3" type="checkbox" id="two" ref="two" name="transfer" value="two" v-model="transfers">
        <label for="two">2 пересадки</label>
        <span @click="only('two')">только</span>
      </div>
      <!-- 3 -->
      <div class="checkbox">
        <input @change="checkbox()" class="transfer transfer-4" type="checkbox" id="three" ref="three" name="transfer" value="three" v-model="transfers">
        <label for="three">3 пересадки</label>
        <span @click="only('three')">только</span>
      </div>
    </div>

  </div>
</template>

<script>
export default {
  name: 'Settings',
  data(){
    return{
      currency: 'rub',
      transfers: []
    }
  },
  methods:{
    // меняем валюту
    changeCurency(cur){
      this.currency = cur;
      this.$emit('changeCurency', cur);
    },
    // выбрать/убрать все фильтры
    checkedAll(){
      // let isActive = document.getElementById('all').checked
      let isActive = this.$refs.all.checked
      var bool = true
      if(!isActive){
        this.transfers = []
        bool = false
      }else{
        this.transfers = ['zero', 'one', 'two', 'three']
      }
      this.refChange(bool, false)
      this.changeFilter()
    },
    // выбор фильтра
    checkbox(){
      // если выбраны все элементы, "checkbox All" становится активным
      if(this.transfers.length > 3){
        this.$refs.all.checked = true
      }else{
        this.$refs.all.checked = false
      }
      this.changeFilter()
    },
    // оставить только один фильтр
    only(id){
      this.refChange(false, true)
      eval(`this.$refs.${id}.checked = true`)
      // document.getElementById(id).checked = true
      this.transfers = [id]
      this.changeFilter()
    },
    // применить фильтр
    changeFilter(){
      var stops = []
      for (var i = 0; i < this.transfers.length; i++) {
        let e = this.transfers[i]
        e == 'zero' ? e = 0 :
        e == 'one' ? e = 1 :
        e == 'two' ? e = 2 :
        e == 'three' ? e = 3 : e = -1
        stops.push(e)
      }
      this.$emit('changeFilter', stops)
    },
    refChange(bool, all){
      if (all) this.$refs.all.checked = bool
      this.$refs.zero.checked = bool
      this.$refs.one.checked = bool
      this.$refs.two.checked = bool
      this.$refs.three.checked = bool
    }
  },
  mounted(){
    // выбираем сразу все фильтры для отображения всех билетов
    this.refChange(true, true)
    this.transfers = ['zero', 'one', 'two', 'three']
  }
}
</script>

<style scoped lang="scss">
@media (min-width: 840px) {
  @import '../assets/style/settings1024.scss';
}
*{
  margin: 0;
  color: #4a4a4a;
  font-family: 'Open Sans', sans-serif;
}
body{
  position: relative;
  margin: 0;
}

@media (min-width: 600px) and (max-width: 840px) {
  @import '../assets/style/settings600.scss';
}

@media (max-width: 600px) {
  @import '../assets/style/settings320.scss';
}
</style>
