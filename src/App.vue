<template>
  <ion-app>
    <main-menu v-if="step == 'main'"></main-menu>
    <div v-else-if="step == 'detail'" class="height-max">
      <detail-table :items-list="parseJson()"></detail-table>
    </div>
  </ion-app>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import { IonApp} from '@ionic/vue'; 
import DetailTable from './views/DetailTable.vue';
import MainMenu from './views/MainMenu.vue';

export default defineComponent({
  name: 'App',
  components: {
    MainMenu,
    DetailTable,
    IonApp
  },
  data(){
    return{
      step:'main',
      decodedString:"",
      idRestaurant:"1",
      numTable:""
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