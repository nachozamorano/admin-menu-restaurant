<template>
  <ion-app>
    <ion-page id="main-content">
        <ion-header>
          <ion-toolbar>
            <ion-buttons slot="start">
              <ion-menu-button class="display-block"></ion-menu-button>
            </ion-buttons>
            <ion-title class="float-left">{{appPages[selectedIndex].title}}</ion-title>
            <ion-icon name="qr-code-outline" class="float-right"></ion-icon>
          </ion-toolbar>
      </ion-header>
    </ion-page>
    <ion-content class="ion-padding">
      <ion-split-pane content-id="main-content">
      <ion-menu content-id="main-content" type="overlay">
          <ion-list id="inbox-list">
            <ion-menu-toggle auto-hide="false" v-for="(p, i) in appPages" :key="i">
              <ion-item @click="selectedIndex = i" router-direction="root" :router-link="p.url" lines="none" detail="false" class="hydrated" :class="{ selected: selectedIndex === i }">
                <ion-icon slot="start" :ios="p.iosIcon" :md="p.mdIcon"></ion-icon>
                <ion-label>{{ p.title }}</ion-label>
              </ion-item>
            </ion-menu-toggle>
          </ion-list>
      </ion-menu>
      <ion-list>
        <TableItem id="main-content" v-for="(data, pos) in tableData" :key="pos" :table-data="data"></TableItem>
      </ion-list>
    </ion-split-pane>
    </ion-content>
  </ion-app>
</template>

<script lang="ts">
import { IonApp, IonContent, IonIcon, IonItem, IonLabel, IonList, IonMenu, IonMenuToggle, IonSplitPane, IonHeader, IonButtons, IonMenuButton, IonTitle} from '@ionic/vue';
import { defineComponent, ref } from 'vue';
import { useRoute } from 'vue-router';
import { HTTP } from './js/http-common';
import { chevronForwardOutline } from 'ionicons/icons';
import TableItem from './views/Table.vue';

export default defineComponent({
  name: 'App',
  components: {
    IonApp, 
    IonContent, 
    IonIcon, 
    IonItem, 
    IonLabel, 
    IonList, 
    IonMenu, 
    IonMenuToggle, 
    TableItem, 
    IonSplitPane,
    IonHeader,
    IonButtons,
    IonMenuButton,
    IonTitle
  },
  data(){
    return{
      errors:[],
      tableData: []
      }
  },
  setup() {
    const selectedIndex = ref(0);
    const appPages = [
      {
        title: 'Todos',
        code: "all",
        url: '/folder/Todos',
        iosIcon: chevronForwardOutline,
        mdIcon: chevronForwardOutline
      },
      {
        title: 'Libres',
        code: "free",
        url: '/folder/Libres',
        iosIcon: chevronForwardOutline,
        mdIcon: chevronForwardOutline
      },
      {
        title: 'Ocupados',
        code: "pend",
        url: '/folder/Ocupados',
        iosIcon: chevronForwardOutline,
        mdIcon: chevronForwardOutline
      }
    ];    
    const path = window.location.pathname.split('folder/')[1];
    if (path !== undefined) {
      selectedIndex.value = appPages.findIndex(page => page.title.toLowerCase() === path.toLowerCase());
    }
    
    const route = useRoute();
    
    return { 
      selectedIndex,
      appPages, 
      chevronForwardOutline,
      isSelected: (url: string) => url === route.path ? 'selected' : ''
    }
  },
  created: function(){
    this.getTable(this.selectedIndex);
  },
  methods: {
    getTable: function (code) {
          this.tableData=[];
          HTTP.post('/api/mesas', {
            id: "1",
            code: this.appPages[code].code
          })
          .then(response => {
            for (const key in response.data) {
              if (Object.hasOwnProperty.call(response.data, key)) {
                const element = response.data[key];
                this.tableData.push({
                  webId:element.IdMesa,
                  CantPersonas:element.cantPersona,
                  numero:element.numero,
                  nombreEstado:element.nombreEstado
                })
              }
            }
          })
          .catch(e => {
            this.errors.push(e)
          })
        },
  },
  watch:{
    selectedIndex: function(value){
      this.getTable(value);
    }
  }
});
</script>