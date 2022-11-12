<template>
    <ion-header :translucent="true">
        <ion-toolbar>
        <ion-title size="large" class="title-step">{{stepInfo.title}}</ion-title>
        </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding size-content index-content height-max">
        <ion-list v-for="(itm, i) in itemsList" :key="i">
        <ion-item-divider class="item-divider-list">
            <ion-label class="mr-3 quantity-detail">{{itm.quantity}}</ion-label>
            <ion-label class="ion-label-list">
            <h1 size="large" class="title-sub">{{itm.name}}</h1>
            <p class="text-description">{{itm.description}}</p>
            <ion-badge color="primary">{{formatPrice(itm.price)+" "+stepInfo.typeMoney}}</ion-badge>
            </ion-label>
            <ion-img :src="errorLoad?notFoundUrl:itm.img" @ionError="notFoundImage" class="size-img"></ion-img>
        </ion-item-divider>
        </ion-list>
    </ion-content>
    <div class="button-div">
        <ion-button @click="cancelClick" color="light"  class="style-back">Cancelar</ion-button>
        <ion-button @click="acceptClick" color="success">Aceptar y Confirmar</ion-button>
    </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { IonLabel, IonImg, IonBadge, IonToolbar, IonHeader, IonContent, IonItemDivider, IonList, IonTitle, IonButton } from '@ionic/vue';
import { HTTP } from '../js/http-common';
export default defineComponent({
    name: 'detail-table',
    components: {
        IonLabel, IonImg, IonBadge, IonToolbar, IonHeader, IonContent, IonItemDivider, IonList, IonTitle, IonButton
    },
    props: {
        'items-list':{
            type: Object,
            default: function () {
                return {}
            }
        }
    },
    data(){
        return{
            errorLoad:false,
            notFoundUrl:"/assets/icon/not_image.png",
            stepInfo: {
                title: "Detalle",
                typeMoney: "CLP"
            },
        }
    },
    methods:{
        notFoundImage: function(){
        this.errorLoad=true;
        },
        formatPrice: function(number: any) {
            if (!number) {
                number = 0;
            }
            return number.toLocaleString('es-CL', { style: 'currency', currency: this.stepInfo.typeMoney });
         },
         cancelClick:function(){
            this.$root.step = "main";
         },
         acceptClick:function(){
            HTTP.post('/api/actualizarEstado', {
                id: this.idRestaurant,
                num: this.numTable,
                status: "3"
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
            this.$root.step = "main";
         }
    }
})
</script>