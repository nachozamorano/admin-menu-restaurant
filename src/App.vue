<template>
  <ion-app v-show="step == 'main' && !isBlocked">
    <main-menu ref ="mainMenu"></main-menu>
    <button class="button" @click="insertData()">
      insertar data
    </button>
  </ion-app>
  <ion-app v-if="step!='main' || isBlocked">
    <div v-if="step == 'detail' && !isBlocked" class="height-max">
      <detail-table :items-list="itemList" :total-amount="totalAmount"></detail-table>
    </div>
    <page-blocked v-else-if="isBlocked" ref="blocked"></page-blocked>
  </ion-app>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { IonApp} from '@ionic/vue'; 
import DetailTable from './views/DetailTable.vue';
import pageBlocked from './views/PageBlocked.vue';
import { HTTP } from './js/http-common';
import MainMenu from './views/MainMenu.vue';

export default defineComponent({
  name: 'App',
  components: {
    MainMenu,
    DetailTable,
    IonApp,
    pageBlocked
  },
  data(){
    return{
      step:'main',
      decodedString:"",
      idRestaurant:"1",
      numTable:"",
      totalAmount:0,
      isBlocked:false,
      rutMesero:"189066937",
      nombre:"",
      subTotal:0,
      itemList:{}
      }
  },
  created: function(){
    HTTP.post('/api/restaurant', {
      id: this.idRestaurant
    })
    .then(response => {
      if(response.data.length != 0){
        var status = response.data[0].FK_idStatus;
        if(status == 1){
          this.isBlocked = false;
        }else if(status == 2){
          this.isBlocked = true;
        }
      }
    })
    .catch(e => {
      this.errors.push(e)
    })
  },  
  watch:{
    step:function(val){
      if(val == "main"){
        this.$refs.mainMenu.selectedIndex = 0;
      }
    }
  },
  methods:{
    parseJson:function(){
      var dataBase = JSON.parse(this.decodedString.split("/dataBase/")[1]);
      this.numTable = dataBase[0].numTable;
      this.subTotal = this.removeAmount(dataBase[0].subTotalAmount);
      this.nombre = dataBase[0].nombre;
      debugger
      this.totalAmount = this.removeAmount(this.subTotal + this.subTotal*0.1);

      return JSON.parse(this.decodedString.split("/dataBase/")[0]);
    },
    removeAmount:function(amount){
      return parseInt(amount.toString().replace(/\./g,""));
    },
    insertData:function(){
      this.$refs.mainMenu.onDecode('[{"id":1,"name":"Cazuela","description":"plato1","price":2500,"img":"https://www.recetaslider.cl/wp-content/uploads/2021/06/principal_5d406d5226f87.jpg","idTipoPlato":1,"quantity":1},{"id":4,"name":"Magdalenas","description":"plato 4","price":3700,"img":"https://content-cocina.lecturas.com/medio/2018/07/19/magdalenas-rellenas-de-chocolate_8848f2fe_800x800.jpg","idTipoPlato":2,"quantity":2}]/dataBase/[{"numTable":"1","subTotalAmount":"9.900","nombre":"1231"}]');
      HTTP.post('/api/orden/insertar', {
              id: this.idRestaurant,
              num: this.numTable,
              total: this.totalAmount,
              rut: this.rutMesero,
              nombre: this.nombre,
              subTotal: this.subTotal,
              listOrder: this.itemList
            })
            .then(response => {
              console.log("insertado");
            })
            .catch(e => {
              this.errors.push(e)
            })
    }
  }
});
</script>