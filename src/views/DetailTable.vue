<template>
    <ion-header :translucent="true">
        <ion-toolbar>
        <ion-title size="large" class="title-step">{{stepInfo.title}}</ion-title>
        </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding size-content index-content height-max">
        <ion-list v-for="(itm, i) in itemsList" :key="i">
        <ion-item-divider class="item-divider-list">
            <ion-label class="mr-3 quantity-detail display-inline-table">{{itm.quantity}}</ion-label>
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
        <ion-button @click="actionClick('5')" color="light"  class="style-back">Cancelar</ion-button>
        <ion-button @click="actionClick('3')" color="success">Aceptar y Confirmar</ion-button>
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
        },
        'total-amount':{
            type: Number,
            default: 0,
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
         actionClick:function(status){
            HTTP.post('/api/mesa/actualizarEstado', {
                id: this.$root.idRestaurant,
                num: this.$root.numTable,
                status: status
            })
            .then(response => {
                this.$root.step = "main";
            })
            .catch(e => {
              this.errors.push(e)
            })
            this.addListOrder();
         },
         addListOrder: function(){
            HTTP.post('/api/orden/insertar', {
                id: this.$root.idRestaurant,
                num: this.$root.numTable,
                total: this.totalAmount + (this.totalAmount*0.1),
                rut:"189066937",
                nombre:"Andrea",
                subTotal: this.totalAmount,
                listOrder: this.listOrder
            })
            .then(response => {
                this.$root.step = "main";
            })
            .catch(e => {
              this.errors.push(e)
            })
         }
    }
})
</script>