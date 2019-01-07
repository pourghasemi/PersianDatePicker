# PersianDatePicker
A JavaScript Datepicker using Vuejs

# Install
```
npm install persian-date --save-dev
```
or 
```
bower install persian-date --save-dev
```
# Usage
```html
<script>
 import VueMoment from './components/VueMoment.vue';
  import persianDate from 'persian-date';
  
  export default {
    name: 'app',
    data() {
      return {
        calender: {
          startDate: {},
          endDate: {},
          language:'en'
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
<template>
  <div id="app">
    <VueMoment v-bind:dates.sync="calender" :value="'start'" :startEnable="startEnable"  :isRange="true" :TowMonth="true" :placeholder="'Start Date'"  />
    <VueMoment v-bind:dates.sync="calender" :value="'end'" :startEnable="startEnable"  :isRange="true" :TowMonth="true" :placeholder="'End Date'"  /> 
   </div>
</template>


```

### Props

| Prop                | Type         | Default                  | Description                                                      |
|---------------------|--------------|--------------------------|------------------------------------------------------------------|
| dates               | Object       | {startDate:{},endDate{},language:'fa'} | dates.startDate ={en:"۲۰۱۹/۲/۱۱", fa:"۱۳۹۷/۱۱/۲۲", language:'fa'} |  
| startEnable         | Object       | Date today               | { 'fa': '','en':''}                                              |
| isRange             | Boolean      | false                    | if true, the type is daterange or datetimerange                  |
| TowMonth            | Booleant     | false                    | if true, show two month                                          |
| placeholder         | String       | 'Date'                   | input placeholder text                                           |
| value               | String       | 'start'                  | if 'start', input value startDate. if 'end', input value endDate |
