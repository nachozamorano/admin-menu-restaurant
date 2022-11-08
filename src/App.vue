<template>
  <main-menu v-if="step == 'main'"></main-menu>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import MainMenu from './views/MainMenu.vue';

export default defineComponent({
  name: 'App',
  components: {
    MainMenu
  },
  data(){
    return{
      step:'main'
      }
  }
});
</script>