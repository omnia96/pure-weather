<template>
  <div class="page">
    <scroll-view class="month-weathers" :scroll-x="true">
      <div class="item" v-for="(item, index) in monthWeatheradta" :key="index">
        <div class="weather animation-slide-left"
             :style="[{'animation-delay': (index+1)*0.2 + 's'}]" >
          <div class="header">
            <div class="date">
              <span>{{item.Date}}</span>
            </div>
            <div class="week">
              <span>{{item.Week}}</span>
            </div>
            <div class="type">
              <span>{{item.Type}}</span>
            </div>
          </div>
          <div class="main">
            <div class="temperature">
              <div class="start flex flex-direction justify-end align-center">
                <span>{{item.Max}}</span>
                <div :style="[{height: maxPercentage.value(item.Max) + '%'}]"></div>
              </div>
              <div class="end flex flex-direction justify-start align-center">
                <div :style="[{height: minPercentage.value(item.Min) + '%'}]"></div>
                <span>{{item.Min}}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </scroll-view>
    <!-- #ifdef MP-WEIXIN -->
    <ad unit-id="adunit-2009e2ee092a6044" ad-type="video" ad-theme="white"></ad>
    <!-- #endif -->
    <!-- #ifdef MP-QQ -->
    <ad unit-id="a7694155dfa8f11edb3d8e7d41767370" type="card"></ad>
    <!-- #endif -->
  </div>
</template>
<script lang="ts">
import Vue from 'vue';
import {StorageService} from '@/core/service/storage.service';
import Component from 'vue-class-component';
import {monthWeatherStorage} from '@/core/config/storage/storage.config';
import {catchError, map, switchMap} from 'rxjs/operators';
import {monthWeather} from '@/core/requests/weather.requests';
import {Percentage} from '@/core/unit/percentage';

@Component({})
export default class MonthWeather extends Vue {
 private monthWeatheradta: Array<any> = [];
 private title = '月天气';
 private cityCode = 0;
 private maxPercentage = new Percentage(0, 0);
 private minPercentage = new Percentage(0, 0);
 public onLoad(option:any) {
   this.cityCode = option.city;
   new StorageService(monthWeatherStorage(String(this.cityCode))).get().pipe(
       catchError(() => this.getdata()),
   ).subscribe((res) => {
     const maxArray: Array<any> = [];
     const minArray: Array<any> = [];
     res.data.forEach((value: any) => {
       maxArray.push(value.Max);
       minArray.push(value.Min);
     });
     this.maxPercentage = new Percentage(Math.max(...maxArray), Math.min(...maxArray));
     this.minPercentage = new Percentage(Math.min(...minArray), Math.max(...minArray));
     this.monthWeatheradta = res.data;
   });
 }
 public getdata() {
   return monthWeather(String(this.cityCode)).pipe(
       map((response) => {
         const data: Array<any> = response.data;
         console.log(data);
         for (const key in data) {
           if (data[key] != null) {
             this.monthWeatheradta.push(
                 {
                   Week: data[key].week.substr(data[key].week.length - 1, 1),
                   Date: data[key].date.split('-')[2],
                   Type: data[key].wea,
                   Max: data[key].tem1,
                   Min: data[key].tem2,
                 },
             );
           }
         }
         return data;
       }),
       switchMap((response) => new StorageService(
           monthWeatherStorage(String(this.cityCode), this.monthWeatheradta),
       ).set()),
   );
 }
}
</script>
<style lang="stylus">
.page
  display flex
  flex-direction column
.month-weathers
  width calc(100vw - 60upx)
  height calc(100vh - 606upx)
  white-space: nowrap;
  margin 30upx
  .item
    display inline-block
    width 100upx
    height 100%
.weather
  width 100%
  height 100%
  display flex
  flex-direction column
  align-items center
  .header
    display flex
    flex-direction column
    align-items center
    .date
    .week
      font-size: 28upx;
      font-weight: bold;
    .type
      writing-mode: tb-rl;
      height: 160upx;
      white-space:nowrap;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 24upx;
  .main
    flex 1
    display flex
    flex-direction column
    align-items center
    position relative
    &::after
      position absolute
      content ""
      bottom 0
      top 0
      border 2upx dashed #cccccc
    .temperature
      position absolute
      top 20upx
      bottom 20upx
      z-index 1
      width 20upx
      display flex
      flex-direction column
      align-items center
      justify-content center
      .start,.end
        width 100%
        height 50%
        div
          width 100%
          background-color #2196f3
        span
          font-weight bold
          color #2196f3
      .start div
        border-radius 10upx 10upx 0 0
      .end div
        border-radius 0 0 10upx 10upx
</style>
