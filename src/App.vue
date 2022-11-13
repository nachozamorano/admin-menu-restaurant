<template>
  <ion-app v-show="step == 'main' && !isBlocked">
    <main-menu ref ="mainMenu"></main-menu>
  </ion-app>
  <ion-app>
    <div v-if="step == 'detail' && !isBlocked" class="height-max">
      <detail-table :items-list="parseJson()"></detail-table>
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
      isBlocked:false
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
      return JSON.parse(this.decodedString.split("/dataBase/")[0]);
    }
  }
});
</script>