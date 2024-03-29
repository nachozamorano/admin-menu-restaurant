<template>
      <ion-page id="main-content">
          <ion-header>
            <ion-toolbar>
              <ion-buttons slot="start">
                <ion-menu-button class="display-block"></ion-menu-button>
              </ion-buttons>
              <ion-title class="float-left">{{appPages[selectedIndex].title}}</ion-title>
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
        <ion-list v-if="appPages[selectedIndex].code != 'qr'">
          <div class="mr-2 refresh-right-icon">
            <ion-icon slot="icon-only" class="cursor-pointer" :icon="refreshOutline" @click="getTable(selectedIndex)"></ion-icon>
          </div>
          <TableItem id="main-content" v-for="(data, pos) in tableData" :key="pos" :table-data="data"></TableItem>
        </ion-list>
        <qrcode-stream v-else @init="onInit" @decode="onDecode"></qrcode-stream>
      </ion-split-pane>
      </ion-content>
  </template>
  
  <script lang="ts">
  import { IonContent, IonIcon, IonItem, IonLabel, IonList, IonMenu, IonMenuToggle, IonSplitPane, IonHeader, IonButtons, IonMenuButton, IonTitle, IonToolbar} from '@ionic/vue'; 
  import { defineComponent, ref } from 'vue';
  import { QrcodeStream } from 'vue3-qrcode-reader'
  import { useRoute } from 'vue-router';
  import { HTTP } from '../js/http-common';
  import { chevronForwardOutline , qrCodeOutline, refreshOutline} from 'ionicons/icons';
  import TableItem from './Table.vue';
  
  export default defineComponent({
    name: 'App',
    components: {
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
      IonTitle,
      IonToolbar,
      QrcodeStream
    },
    data(){
      return{
        errors:[],
        tableData: [],
        error: '',
        torch: false,
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
          title: 'Tomar Pedido',
          code: "pend",
          url: '/folder/Tomar pedido',
          iosIcon: chevronForwardOutline,
          mdIcon: chevronForwardOutline
        },
        {
          title: 'Pagar',
          code: "pay",
          url: '/folder/Pagar',
          iosIcon: chevronForwardOutline,
          mdIcon: chevronForwardOutline
        },
        {
          title: 'Capturar QR',
          code: "qr",
          url: '/folder/Qr',
          iosIcon: qrCodeOutline,
          mdIcon: qrCodeOutline
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
        refreshOutline,
        isSelected: (url: string) => url === route.path ? 'selected' : ''
      }
    },
    created: function(){
      this.getTable(this.selectedIndex);
    },
    methods: {
        async onInit( promise ) {
            try {
                const { capabilities } = await promise
                // successfully initialized
            } catch (error) {
                if (error.name === 'NotAllowedError') {
                    this.error = "user denied camera access permisson"
                } else if (error.name === 'NotFoundError') {
                    this.error = "no suitable camera device installed"
                } else if (error.name === 'NotSupportedError') {
                    this.error = "page is not served over HTTPS (or localhost)"
                } else if (error.name === 'NotReadableError') {
                    this.error = "maybe camera is already in use"
                } else if (error.name === 'OverconstrainedError') {
                    this.error = " did you requested the front camera although there is none?"
                } else if (error.name === 'StreamApiNotSupportedError') {
                    this.error = "browser seems to be lacking features"
                }
            } finally {
                // hide loading indicator
            }
        },
        onDecode(decodedString) {
            this.$root.decodedString = decodedString;
            this.$root.itemList = this.$root.parseJson();
            this.$root.step = "detail";
            
        },
        getTable: function (code) {
            this.tableData=[];
            HTTP.post('/api/mesa', {
              id: this.$root.idRestaurant,
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