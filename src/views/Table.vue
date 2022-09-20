<template>
  <ion-card>
    <ion-card-header>
    <ion-card-title class="float-left">{{ "Mesa N°"+tableData["numero"] }}</ion-card-title>
    <ion-card-subtitle class="float-right ml-20 top-less-5">{{tableData["nombreEstado"]}}</ion-card-subtitle>
    </ion-card-header>

    <ion-card-content>
      <ion-card-subtitle class="float-left mt-20 clear-left">{{"Capacidad: "+tableData["numero"]}}</ion-card-subtitle>
    </ion-card-content>
  </ion-card>
</template>

<script lang="ts">
import { IonCard, IonCardContent, IonCardHeader, IonCardTitle, IonCardSubtitle } from '@ionic/vue';
import { warning } from 'ionicons/icons';
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'TableItem',
  components: { IonCard, IonCardContent, IonCardHeader, IonCardTitle, IonCardSubtitle },
  setup() {
    return { warning };
  },
  props:{
    'tableData':{
      type:Array,
      default: function () {
        return []
      }
    }
  },
});
</script>