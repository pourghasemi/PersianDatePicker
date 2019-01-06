<template>
  <div id="app">
    <VueMoment v-bind:dates.sync="calender" :value="'start'" :startEnable="startEnable"  :isRange="true" :TowMonth="true" :placeholder="'Start Date'" :lang="'fa'" />
    <VueMoment v-bind:dates.sync="calender" :value="'end'" :startEnable="startEnable"  :isRange="true" :TowMonth="true" :placeholder="'End Date'" :lang="'fa'" /> 
   </div>
</template>

<script>
  import VueMoment from './components/VueMoment.vue'
  import persianDate from 'persian-date';
  
  export default {
    name: 'app',
    data() {
      return {
        calender: {
          startDate: {},
          endDate: {}
        },
        startEnable: {
          'fa': new persianDate().toArray(),
          'en': new persianDate(new Date()).toCalendar('gregorian').toArray()
        },

      }
    },
    components: {
      VueMoment
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
