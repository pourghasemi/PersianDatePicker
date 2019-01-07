<template>
  <div class="persianDate">
    <input v-if="value=='start'" type="text" v-model="dates.language === 'fa' ? dates.startDate.fa : dates.startDate.en" readonly :placeholder="placeholder" @click="visible=true">
    <input v-if="value=='end'" type="text" v-model="dates.language === 'fa' ? dates.endDate.fa : dates.endDate.en" readonly :placeholder="placeholder" @click="visible=true">
    <div :class="visible ? 'persianDate-box':''">
      <span class="close" v-if="isMobile" @click="visible=false">
              <svg id="common-icon-x-icon_yD_mH" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 9.85 9.93" class="x-icon-x" width="100%" height="100%">
                <path style="fill:none;stroke:currentColor;stroke-miterlimit:10" d="M.71.79l8.43 8.43M9.14.71L.71 9.14"></path>
              </svg>
            </span>
      <div :class="['persianDate_dropdown',dates.language === 'fa' ? 'rtl' : 'ltr', visible? 'effect':'']">
        <div class="head">
          <a @click="prevMonth()" class=" arrow">
            <</a>
              <div class="month-select">
                <div>
                  <select @change="changeYear()" v-model="calenders[0].date[0]">
                            <option :value="year" v-for="year in yearsArray[dates.language]" :key="year">{{year}}</option>
                          </select>
                  <b/>
                  <select @change="changeMonth()" v-model="calenders[0].date[1]">
                            <option :value="index+1" v-for="(month, index) in months[dates.language]" :key="month">{{month}}</option>
                          </select>
                </div>
                <div v-if="showTowMonth">
                  <span>{{calenders[1].date[0]}} {{months[dates.language][calenders[1].date[1]-1]}}</span>
                </div>
              </div>
              <a @click="nextMonth()" class=" arrow">></a>
        </div>
  
  
        <div class="calenders" :class="dates.language === 'fa' ? 'rtl' : 'ltr'" @mouseleave="hoverDate=''">
  
          <div :class="['body', showTowMonth && !index? 'border-left':'']" v-for="(calender, index) in calenders">
            <div class="body_header">
              <span class="day-cell" v-for="item in rangeName[dates.language]" :key="item+'_'">
                            {{item}}
                          </span>
            </div>
  
            <div v-for=" (val,index) in calender.arrayDays" :key="index+'___'">
              <span :class="[
                            'day-cell',
                            obj.value && isDisable([calender.date[0],calender.date[1],obj.value], [startEnable[dates.language][0], startEnable[dates.language][1], startEnable[dates.language][2] ]) ? 'disable' : '',
                            obj.unix==dates.startDate.unix ||obj.unix==dates.endDate.unix || (isRange && dates.startDate.unix && hoverDate==obj.unix) ? 'activeCell' :'',
                            (!isRange && hoverDate && hoverDate==obj.unix)||
                            (isRange && hoverDate && hoverDate==obj.unix && !dates.startDate.unix && !dates.endDateunix) ||
                            (value=='end' && isRange && hoverDate && dates.startDate.unix  && !dates.endDate.unix && obj.unix < hoverDate && dates.startDate.unix < obj.unix) || 
                            (isRange && dates.startDate.unix  && dates.endDate.unix && dates.startDate.unix<obj.unix && dates.endDate.unix> obj.unix) ? 'hoverCell':'',
                            obj.value && obj.unix==getUnix([today[0], today[1], today[2]])? 'today':'',
                            obj.value ? '' : 'null'
                          ]" @mouseover="hover([calender.date[0], calender.date[1], obj.value])" v-for=" (obj,i) in calender.arrayDays[index]" :key="i+'___'" @click="selectDate(obj.value,calender.date)">{{obj.value}}</span>
            </div>
          </div>
        </div>
        <div class="footer">
  
          <div class="clear" @click="reset">
            {{dates.language === 'en'? 'Clear':'پاک کردن'}}
          </div>
  
          <div>
            <a @click="ChangeLang('en')" v-if="dates.language === 'fa'">میلادی</a>
            <a @click="ChangeLang('fa')" v-if="dates.language === 'en'">شمسی</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import Vue from 'vue';
  import persianDate from 'persian-date';
  persianDate.toLocale('en');
  
  export default {
    props: {
      startEnable: {
        default () {
          return {
            'fa': new persianDate().toArray(),
            'en': new persianDate(new Date()).toCalendar('gregorian').toArray()
          }
        },
        type: Object
      },
      isRange: {
        default: false,
        type: Boolean
      },
      TowMonth: {
        default: true,
        type: Boolean
      },
      placeholder: {
        default: 'Date',
        type: String
      },
      diff: {
        default: 1,
        type: Number
      },
      dates: {
        default () {
          return {
            startDate: {},
            endDate: {},
            lang: 'fa'
          }
        },
        type: Object
      },
      value: {
        default: 'start',
        type: String
      }
  
    },
    data() {
      return {
        isMobile: false,
        showTowMonth: false,
        hoverDate: '',
        visible: false,
        date: new persianDate().toArray(),
        day: '',
        Array: [],
        calenders: this.getCalender(),
        yearSelected: '',
        yearsArray: {
          'fa': Array(15).fill(0).map((e, i) => i + Number(new persianDate().toCalendar('persian').toLocale('en').format('YYYY'))),
          'en': Array(15).fill(0).map((e, i) => i + Number(new Date().getFullYear()))
        },
        showYears: false,
        rangeName: {
          'fa': ["ش", "ی", "د", "س", "چ", "پ", "ج"],
          'en': ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']
        },
        months: {
          'fa': ["فروردین", "اردیبهشت", "خرداد", "تیر", "مرداد", "شهریور", "مهر", "آبان", "آذر", "دی", "بهمن", "اسفند"],
          'en': ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"]
        }
      }
    },
    computed: {
      today() {
        return this.DateNow();
      }
    },
    mounted() {
      this.updateDate();
    },
    watch: {
      'dates.language': function(newVal) {
        this.ChangeLang(newVal);
        this.updateDate();
      }
    },
  
    methods: {
      getCalender() {
        this.checkMobile();
        if (this.showTowMonth) {
          return (
            [{
                date: this.DateNow(),
                arrayDays: [],
              },
              {
                date: '',
                arrayDays: []
              }
            ]
          )
        } else {
          return (
            [{
              date: this.DateNow(),
              arrayDays: [],
            }]
          )
        }
      },
      newDate(date) {
        if (this.dates.language === 'fa') {
          persianDate.toCalendar('persian')
          return new persianDate([date[0], date[1], date[2]])
        } else {
          persianDate.toCalendar('gregorian')
          return new persianDate(new Date(date[0], date[1] - 1, date[2]))
        }
      },
  
      DateNow() {
        if (this.dates.language === 'fa') {
          persianDate.toCalendar('persian')
          return new persianDate().toArray()
        } else {
          persianDate.toCalendar('gregorian')
          return new persianDate(new Date()).toArray()
        }
      },
      updateDate() {
        this.cellRender(this.calenders[0].date, this.calenders[0]);
        if (this.showTowMonth) {
          this.calenders[1].date = this.newDate(this.calenders[0].date).add('M', 1).toArray()
          this.cellRender(this.calenders[1].date, this.calenders[1]);
        }
      },
      getDays(date) {
        if (this.dates.language === 'fa')
          return new persianDate([date[0], date[1], date[2]]).daysInMonth()
        else
          return new Date(date[0], date[1], 0).getDate();
      },
      selectDate(day, date) {
        if (this.value == 'end') {
          if (!this.dates.startDate.unix || (this.dates.startDate.unix && this.getUnix([date[0], date[1], day]) > this.dates.startDate.unix)) {
            
            this.dates.endDate = this.selectRender(day, date);
          }
        } else if (this.value == 'start') {
          if (!this.dates.endDate.unix || (this.getUnix([date[0], date[1], day]) < this.dates.endDate.unix)) {
            this.dates.startDate = this.selectRender(day, date);
          }
        }
        if(this.dates.startDate.unix && this.dates.endDate.unix){
          const end=new persianDate(new Date(this.dates.endDate.en));
          const start=new persianDate(new Date(this.dates.startDate.en));
          let diffrent = end.diff(start, 'days');
          if(diffrent < this.diff){
            this.dates.endDate={};
            alert('diff = 3');
          }
        }
        this.visible = false;

      },
      selectRender(day, date, item) {
        const now = this.startEnable[this.dates.language];
        if (!(this.isDisable([date[0], date[1], day], [now[0], now[1], now[2]]))) {
          if (this.dates.language == 'fa') {
            persianDate.toCalendar('persian');
            return {
              'fa': new persianDate([date[0], date[1], day]).format('YYYY/M/D'),
              'unix': this.getUnix([date[0], date[1], day]),
              'en': new persianDate([date[0], date[1], day]).toCalendar('gregorian').format('YYYY/M/D')
  
            }
          } else {
            persianDate.toCalendar('gregorian');
            return {
              'en': new persianDate([date[0], date[1], day]).format('YYYY/M/D'),
              'fa': new persianDate([date[0], date[1], day]).toCalendar('persian').format('YYYY/M/D'),
              'unix': this.getUnix([date[0], date[1], day]),
  
  
            }
          }
        }
      },
      nextMonth() {
        this.calenders[0].date = this.newDate(this.calenders[0].date).add('M', 1).toArray();
        this.updateDate();
      },
      prevMonth() {
        if (!(this.calenders[0].date[0] == this.startEnable[this.dates.language][0] && this.calenders[0].date[1] == this.startEnable[this.dates.language][1])) {
          this.calenders[0].date = this.newDate(this.calenders[0].date).subtract('M', 1).toArray();
          this.updateDate();
        }
      },
      ChangeLang(lang) {
        this.dates.language = lang;
        this.calenders[0].date = this.DateNow();
        this.updateDate();
      },
      changeYear() {
        this.calenders[0].date = this.newDate(this.calenders[0].date).toArray();
        this.updateDate();
      },
      changeMonth() {
        this.calenders[0].date = this.newDate(this.calenders[0].date).toArray();
        this.updateDate();
      },
      isDisable(arr1, arr2) {
        const a = this.getUnix(arr1);
        const b = this.getUnix(arr2);
  
        if (a >= b)
          return false;
        else
          return true
  
      },
      getUnix(date) {
        if (this.dates.language == 'en') {
          return new persianDate(new Date(date)).unix();
        }
        return new persianDate(date).unix();
      },
      hover(date) {
        this.hoverDate = this.getUnix(date);
      },
      cellRender(date, obj) {
        const days = this.getDays(date);
        const day = this.newDate([date[0], date[1], 1]).day();
        const count = day ? day - 1 : day;
        obj.arrayDays = new Array(Math.ceil((count + days) / 7)).fill(new Array(7));
  
        obj.arrayDays[0] = [...(Array(day - 1).fill({}))]
        obj.arrayDays[0].push(...Array.from({
          length: 8 - day
        }, (v, k) => {
          return {
            'value': (k + 1),
            'unix': this.getUnix([obj.date[0], obj.date[1], k + 1])
          }
        }))
  
        obj.arrayDays.forEach((arr, index) => {
          if (index) {
            obj.arrayDays[index] = [...Array.from({
              length: 7
            }, (v, k) => {
              const val = index * 7 - day + k + 2;
              if (val <= days) {
                return {
                  'value': val,
                  'unix': this.getUnix([obj.date[0], obj.date[1], val])
                };
              }
              return {
                'value': null
              }
            })]
          }
        });
      },
      reset() {
        this.dates.startDate = {};
        this.dates.endDate = {}; 
      },
      checkMobile() {
        this.showTowMonth = this.TowMonth;
        if (window.innerWidth <= 600) {
          this.isMobile = true;
          this.showTowMonth = false;
        }
      }
    },
    created() {
      let self = this;
      this.checkMobile();
  
  
      window.addEventListener('click', (e) => {
        // close dropdown when clicked outside
        if (!self.$el.contains(e.target)) {
          self.visible = false;
        }
      })
    }
  
  }
</script>

<style>
  .persianDate {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    position: relative;
    display: inline-block;
  }
  
  .persianDate .rtl {
    direction: rtl;
    text-align: right;
  }
  
  .persianDate .ltr {
    direction: ltr;
    text-align: left;
  }
  
  .persianDate input {
    line-height: 30px;
    border-radius: 4px;
    text-align: right;
    padding: 0px 5px;
    -webkit-box-shadow: 0 0;
    box-shadow: 0 0;
    outline-offset: -2px !important;
  }
  
  .persianDate input:focus {
    outline: 0px !important;
  }
  
  .persianDate .close {
    display: none;
  }
  
  .persianDate .persianDate_dropdown {
    position: absolute;
    top: 100%;
    right: 0;
    left: auto;
    font-size: 14px;
    border-radius: 4px;
    background: #fff;
    -webkit-box-shadow: 0 -1px 1px 0 rgba(0, 0, 0, 0.05), 0 1px 5px 0 rgba(0, 0, 0, 0.15);
    box-shadow: 0 -1px 1px 0 rgba(0, 0, 0, 0.05), 0 1px 5px 0 rgba(0, 0, 0, 0.15);
    max-height: 0;
    max-width: 0;
    overflow: hidden;
    display: grid;
    opacity: 0;
    z-index: 9;
  }
  
  .persianDate .persianDate_dropdown.effect {
    -webkit-transition: all .25s;
    transition: all .25s;
    -webkit-transition-timing-function: ease-in;
    transition-timing-function: ease-in;
    max-height: 430px;
    max-width: 700px;
    opacity: 1;
  }
  
  .persianDate .persianDate_dropdown .head {
    text-align: center;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    background: -webkit-gradient(linear, left top, left bottom, from(rgba(255, 255, 255, 0.18)), to(#f1f1f1));
    background: linear-gradient(-180deg, rgba(255, 255, 255, 0.18) 0%, #f1f1f1 100%);
    padding: 9px 2px;
  }
  
  .persianDate .persianDate_dropdown .head .arrow {
    width: 50px;
    font-weight: bold;
    text-shadow: 1px 0px;
    color: orange;
  }
  
  .persianDate .persianDate_dropdown .head .month-select {
    -webkit-box-flex: 1;
    -ms-flex: 1;
    flex: 1;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
  }
  
  .persianDate .persianDate_dropdown .head .month-select>* {
    -webkit-box-flex: 1;
    -ms-flex: 1;
    flex: 1;
  }
  
  .persianDate .persianDate_dropdown .head .month-select select {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: transparent;
    border: 0px;
    font-size: 14px;
  }
  
  .persianDate .persianDate_dropdown .head .month-select select:focus {
    outline: 0px !important;
  }
  
  .persianDate .persianDate_dropdown .calenders {
    display: -webkit-inline-box;
    display: -ms-inline-flexbox;
    display: inline-flex;
    padding: 5px;
  }
  
  .persianDate .persianDate_dropdown .calenders .body {
    padding: 4px;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .body_header {
    display: -webkit-inline-box;
    display: -ms-inline-flexbox;
    display: inline-flex;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .body_header .day-cell {
    font-weight: bold;
    color: #656565;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell {
    width: 35px;
    line-height: 35px;
    text-align: center;
    display: inline-block;
    margin: 3px;
    cursor: pointer;
    border-radius: 2px;
    overflow: hidden;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell.disable {
    color: #bfbfbf;
    cursor: no-drop;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell.activeCell {
    background: #ff8100;
    color: #fff;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell.hoverCell {
    background: #ffe4c9;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell.null {
    background: transparent;
    border-color: transparent;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell.today {
    position: relative;
  }
  
  .persianDate .persianDate_dropdown .calenders .body .day-cell.today:after {
    position: absolute;
    content: '';
    width: 7px;
    height: 7px;
    left: -3px;
    top: -3px;
    display: inline-block;
    background: red;
    -webkit-transform: rotate(45deg);
    transform: rotate(45deg);
  }
  
  .persianDate .persianDate_dropdown .footer {
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    padding: 5px 10px;
    font-size: 13px;
    background: #fbfbfb;
  }
  
  .persianDate .persianDate_dropdown .footer .clear {
    -webkit-box-flex: 1;
    -ms-flex: 1;
    flex: 1;
  }
  
  .persianDate .rtl.persianDate_dropdown .calenders .body.border-left {
    border-left: solid 1px #dee2e6;
  }
  
  .persianDate .ltr.persianDate_dropdown .calenders .body.border-left {
    border-right: solid 1px #dee2e6;
  }
  
  .persianDate a {
    cursor: pointer;
  }
  
  .persianDate b {
    color: #656565;
  }
  
  @media only screen and (max-width: 600px) {
    .persianDate .persianDate-box {
      position: fixed;
      top: 0;
      bottom: 0;
      left: 0;
      right: 0;
      background: #fff;
      z-index: 9;
      -webkit-box-align: center;
      -ms-flex-align: center;
      align-items: center;
      vertical-align: center;
      display: -webkit-box;
      display: -ms-flexbox;
      display: flex;
      -webkit-box-pack: center;
      -ms-flex-pack: center;
      justify-content: center;
    }
    .persianDate .persianDate-box .close {
      display: inline;
      position: absolute;
      right: 15px;
      top: 15px;
      padding: 10px;
    }
    .persianDate .persianDate-box .close svg {
      width: 20px;
    }
    .persianDate .persianDate_dropdown {
      position: relative;
      top: auto !important;
      left: auto !important;
      right: auto !important;
      display: inline-block;
    }
  }
</style>