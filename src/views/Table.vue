<template>
  <ion-card>
    <ion-card-header>
      <ion-card-subtitle>Card Subtitle</ion-card-subtitle>
      <ion-card-title>{{ $route.params.id }}</ion-card-title>
    </ion-card-header>

    <ion-card-content>
      Keep close to Nature's heart... and break clear away, once in awhile,
      and climb a mountain or spend a week in the woods. Wash your spirit clean.
    </ion-card-content>
  </ion-card>
</template>

<script lang="ts">
import { IonCard, IonCardContent, IonCardHeader, IonCardSubtitle, IonCardTitle } from '@ionic/vue';
import { warning } from 'ionicons/icons';
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'TableItem',
  components: { IonCard, IonCardContent, IonCardHeader, IonCardSubtitle, IonCardTitle },
  setup() {
    return { warning };
  }
});
</script>