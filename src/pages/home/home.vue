<template>
  <div class="page">
    <div class="status-bar" :style="'height:' + StatusBarHeight + 'px;'"></div>
    <div class="container">
      <div class="header"
            :style="'margin-top:calc(0rpx + '+StatusBarHeight+'px);'">
        <view class="icon">
          <icon-freecns-component-vue icon="Cumulus-Cloud" color="#2196f3" size="20px"/>
        </view>
        <view class="name">Pure · 简天气</view>
        <view class="current">首页</view>
      </div>
      <div @click="toSearch">
        <md-search></md-search>
      </div>
      <swiper class="u-skeleton"
              :style="'width:100%;height:calc(100vh - 105px - ' + StatusBarHeight + 'px);'"
              :current="MainSwiper"
              @animationfinish="MainSwiperChange">
        <swiper-item v-for="(value,key) in StarCityList" v-bind:key="key">
          <scroll-view scroll-y style="width: 100%; height: 100%;">
            <view class="flex flex-direction align-center h-100">
              <view class="card">
                <view class="header">
                  <view>今日天气</view>
                  <view class="address">
                    <view class="left" v-if="Address.icon">
                      <icon-awesome-component-vue icon="fa-map-marker"/>
                    </view>
                    <view class="right"
                          v-if="Address.icon">{{ Address.district }}·{{ Address.street }}</view>
                    <view class="right" v-else>
                      {{ Address.province != Address.city ? Address.province + '·' : '' }}
                      {{ Address.city != Address.district ? Address.city + '·' : '' }}
                      {{ Address.district }}
                    </view>
                  </view>
                  <view>{{ Today.month }}月{{ Today.date }}日</view>
                </view>
                <view class="main today-weather u-skeleton-rect"
                      :style="CityImage != null? 'background-image: url('+CityImage+');':''">
                  <view class="start">
                    <view class="temperature">
                      <view class="status">
                        <view :style="TemperatureStatus? 'background:#2196f3;':''" :data-id="true"
                              @tap="SwitchTemperatureStatus">实测
                        </view>
                        <view :style="!TemperatureStatus? 'background:#2196f3;':''" :data-id="false"
                              @tap="SwitchTemperatureStatus">体感
                        </view>
                      </view>
                      <view class="value">
                        <view v-if="TemperatureStatus">
                          {{ realTimeWeather.temperature.current }}°
                        </view>
                        <view v-if="!TemperatureStatus">
                          {{ realTimeWeather.temperature.Somatosensory }}°
                        </view>
                      </view>
                    </view>
                    <view class="type">
                      <view class="title">{{ realTimeWeather.weather.type }}</view>
                      <view class="temperature">
                        <view style="color: #F0AD4E;">{{ realTimeWeather.temperature.Max }}°</view>
                        <view style="color: #2196f3;">{{ realTimeWeather.temperature.Min }}°</view>
                      </view>
                    </view>
                  </view>
                  <view class="end">
                    <view class="item">{{ realTimeWeather.wind.type }}</view>
                    <view class="item">{{ realTimeWeather.wind.meter }}</view>
                    <view class="item">{{ realTimeWeather.wind.speed }}</view>
                  </view>
                </view>
              </view>
<!--              <view class="card">-->
<!--                <view class="header u-skeleton-rect">-->
<!--                  <text>今日空气</text>-->
<!--                  <text>{{ RealTimeWeather.Air.Level }}</text>-->
<!--                </view>-->
<!--                <view class="main today-air u-skeleton-rect">-->
<!--                  <view class="item animation-slide-left" style="animation-delay: 0.3s">-->
<!--                    <view class="title">Pm2.5</view>-->
<!--                    <view class="content">{{ RealTimeWeather.Air.Pm25 }}</view>-->
<!--                  </view>-->
<!--                  <view class="item animation-slide-left" style="animation-delay: 0.6s">-->
<!--                    <view class="title">湿度</view>-->
<!--                    <view class="content">{{ RealTimeWeather.Air.Humidity }}</view>-->
<!--                  </view>-->
<!--                  <view class="item animation-slide-left" style="animation-delay: 0.9s">-->
<!--                    <view class="title">气压hPa</view>-->
<!--                    <view class="content">{{ RealTimeWeather.Air.Pressure }}</view>-->
<!--                  </view>-->
<!--                  <view class="item animation-slide-left" style="animation-delay: 1.2s">-->
<!--                    <view class="title">能见度</view>-->
<!--                    <view class="content">{{ RealTimeWeather.Air.Visibility }}</view>-->
<!--                  </view>-->
<!--                </view>-->
<!--              </view>-->
<!--              <view class="card">-->
<!--                <view class="header">-->
<!--                  <text>Tips</text>-->
<!--                </view>-->
<!--                <view class="main today-tips u-skeleton-rect">-->
<!--                  <text>{{ RealTimeWeather.Air.Tips }}</text>-->
<!--                </view>-->
<!--              </view>-->
<!--              <view class="card">-->
<!--                <view class="header">每小时</view>-->
<!--                <view class="main u-skeleton-rect">-->
<!--                  <scroll-view class="hours" scroll-x="true">-->
<!--                    <template v-for="(item,index) in OneWeekWeather[0].Hours">-->
<!--                      <view class="item" :key="index"-->
<!--                            v-if="-->
<!--                              item.hours >= 8 &&-->
<!--                              Today.date == OneWeekWeather[0].Date.Date &&-->
<!--                              item.hours >= Today.hours">-->
<!--                      <view class="title">{{ item.title }}</view>-->
<!--                      <view class="weather">{{ item.wea }}</view>-->
<!--                      <view class="tem">{{ item.tem }}</view>-->
<!--                    </view>-->
<!--                    </template>-->
<!--                    <view class="item date" v-if="Today.date == OneWeekWeather[0].Date.Date">-->
<!--                      {{ OneWeekWeather[1].Date.Date }}-->
<!--                    </view>-->
<!--                    <template v-for="(item,index) in OneWeekWeather[0].Hours">-->
<!--                      <view class="item" :key="index"-->
<!--                            v-if="-->
<!--                              item.hours < 8  &&-->
<!--                              (-->
<!--                                Today.date == OneWeekWeather[0].Date.Date ||-->
<!--                                item.hours >= Today.hours-->
<!--                              )-->
<!--                            ">-->
<!--                      <view class="title">{{ item.title }}</view>-->
<!--                      <view class="weather">{{ item.wea }}</view>-->
<!--                      <view class="tem">{{ item.tem }}</view>-->
<!--                    </view>-->
<!--                    </template>-->
<!--                    <template v-for="(item,index) in OneWeekWeather[1].Hours">-->
<!--                      <view class="item" :key="index"-->
<!--                          v-if="index < 16">-->
<!--                      <view class="title">{{ item.title }}</view>-->
<!--                      <view class="weather">{{ item.wea }}</view>-->
<!--                      <view class="tem">{{ item.tem }}</view>-->
<!--                    </view>-->
<!--                    </template>-->
<!--                    <view class="item date">{{ OneWeekWeather[2].Date.Date }}</view>-->
<!--                    <template v-for="(item,index) in OneWeekWeather[1].Hours">-->
<!--                      <view class="item" :key="index"-->
<!--                          v-if="index >= 16">-->
<!--                      <view class="title">{{ item.title }}</view>-->
<!--                      <view class="weather">{{ item.wea }}</view>-->
<!--                      <view class="tem">{{ item.tem }}</view>-->
<!--                    </view>-->
<!--                    </template>-->
<!--                    <template v-for="(item,index) in OneWeekWeather[2].Hours">-->
<!--                      <view class="item" :key="index"-->
<!--                          v-if="index < 16">-->
<!--                      <view class="title">{{ item.title }}</view>-->
<!--                      <view class="weather">{{ item.wea }}</view>-->
<!--                      <view class="tem">{{ item.tem }}</view>-->
<!--                    </view>-->
<!--                    </template>-->
<!--                  </scroll-view>-->
<!--                </view>-->
<!--              </view>-->
<!--              <view class="card">-->
<!--                <view class="header">-->
<!--                  <view>一周天气</view>-->
<!--                  <view @click="more(key)">更多</view>-->
<!--                </view>-->
<!--                <view class="main week-weather u-skeleton-rect">-->
<!--                  <view class="item animation-slide-left"-->
<!--                        v-for="(item,index) in OneWeekWeather" :key="index"-->
<!--                        :style="[{-->
<!--                          'animation-delay': (index+1)*0.2 + 's',-->
<!--                           color: Today.date == item.Date.Date? '#2196f3':'#000000'-->
<!--                        }]">-->
<!--                    <view :style="Today.date == item.Date.Date? 'color:#2196f3;':'color:#9e9e9e;'">-->
<!--                      {{ item.Date.Week }}-->
<!--                    </view>-->
<!--                    <view>{{ item.Date.Date }}</view>-->
<!--                    <view class="weather">{{ item.Weather.Type }}</view>-->
<!--                    <view>{{ item.Temperature.Max }}</view>-->
<!--                    <view>{{ item.Temperature.Min }}</view>-->
<!--                  </view>-->
<!--                </view>-->
<!--              </view>-->
<!--              <view class="card">-->
<!--                <view class="header">-->
<!--                  <view>指数</view>-->
<!--                </view>-->
<!--                <view class="main index u-skeleton-rect">-->
<!--                  <view class="item"-->
<!--                        v-for="(item,index) in OneWeekWeather[0].Index" :key="index" :id="item.desc"-->
<!--                        @click="ShowIndexDesc(item.desc)">-->
<!--                    <view class="icon">-->
<!--                      <icon-awesome-component-vue :icon="item.icon" size="20px"/>-->
<!--                    </view>-->
<!--                    <view class="title">{{ item.title }}</view>-->
<!--                    <view class="level">{{ item.level }}</view>-->
<!--                  </view>-->
<!--                </view>-->
<!--              </view>-->
<!--              <view class="card">-->
<!--                <view class="header">-->
<!--                  <view>广告</view>-->
<!--                </view>-->
<!--                <view class="main ad-bar">-->
<!--                  &lt;!&ndash; #ifdef MP-WEIXIN &ndash;&gt;-->
<!--                  <ad unit-id="adunit-86ce23bbd3c7393e"></ad>-->
<!--                  &lt;!&ndash; #endif &ndash;&gt;-->
<!--                  &lt;!&ndash; #ifdef MP-QQ &ndash;&gt;-->
<!--                  <ad unit-id="c5e6d691147cc2f04445eb0b5c84bf90" type="card"></ad>-->
<!--                  &lt;!&ndash; #endif &ndash;&gt;-->
<!--                </view>-->
<!--              </view>-->
              <view class="copyright">Powered By Omnia96</view>
            </view>
          </scroll-view>
        </swiper-item>
      </swiper>
    </div>
<!--    <navigation-component-vue :StatusBarHeight="StatusBarHeight"-->
<!--                              :NavigationBarItems="NavigationBarItems"/>-->
    <u-skeleton :loading="StartupStatus" :animation="true" bgColor="#FFF"></u-skeleton>
  </div>
</template>
<script lang="ts">
import Vue from 'vue';
import IconAwesomeComponentVue from '../../components/IconAwesome/IconAwesome.component.vue';
import NavigationComponentVue from '../../components/Navigation/Navigation.component.vue';
import IconFreecnsComponentVue from '../../components/IconFreecns/IconFreecns.component.vue';
import {systemInfoService} from '@/core/service/core/core.module';
import {StorageService} from '@/core/service/storage.service';
import StartUpComponentVue from '../../components/StartUp/StartUp.component.vue';
import Component from 'vue-class-component';
import {of, zip} from 'rxjs';
import {
  realTimeWeatherStorage,
  starCitiesStorage,
  weekWeatherStorage,
} from '@/core/config/storage/storage.config';
import {catchError, finalize, map, switchMap} from 'rxjs/operators';
import {realTimeWeather, weekWeather} from '@/core/requests/weather.requests';
import {StorageValue} from '@/core/models/storageValue';
import {AddressService} from '@/core/service/address.service';
import MdSearch from '@/components/md-search/md-search.vue';
import RealTimeWeather from '@/core/models/realTimeWeather';
// #ifdef MP-WEIXIN
let interstitialAd: any = {};
declare const wx: any;
// #endif
@Component({components: {
  MdSearch,
  IconAwesomeComponentVue,
  IconFreecnsComponentVue,
  NavigationComponentVue,
  StartUpComponentVue,
}})
export default class Home extends Vue {
  private StatusBarHeight = systemInfoService.systemInfo.statusBarHeight;
  private Today: any = {};
  private Address: any = {};
  private StartupStatus = true;
  private realTimeWeather: RealTimeWeather = new RealTimeWeather();
  private OneWeekWeather = [
    {
      Date: {},
      Hours: [],
      Weather: {},
    },
    {Hours: [], Weather: {}},
    {Date: {}, Weather: {}},
  ];
  private CityImage = null;
  private TemperatureStatus = true;
  private MainSwiper = 0;
  private StarCityList = {} || undefined;
  private NavigationBarItems = [
    {icon: 'fa-home', title: '首页', path: '../home/home', selected: true},
    {icon: 'fa-exclamation-circle', title: '关于', path: '../about/about', selected: false},
  ];
  public onLoad() {
    this.GetCityCode();
    // #ifdef MP-QQ
    uni.hideTabBar();
    // #endif
    // #ifdef MP-WEIXIN
    // 在页面onLoad回调事件中创建插屏广告实例
    this.CreateAd();
    // #endif
    // uni.getSystemInfo({
    //   success: (result) => {
    //     console.log(result.safeArea);
    //   },
    // });
  }
  public onReady() {
    // #ifdef MP-WEIXIN
    // 在适合的场景显示插屏广告
    this.ShowAds();
    // #endif
  }
  public onShow() {
    this.SetToday();
    this.MainSwiper = 0;
    new StorageService(starCitiesStorage).get().subscribe(
        (res) => {
          this.StarCityList = res.data;
        },
        () => {
          this.StarCityList = [{}];
        },
    );
  }
  public onHide() {
    this.MainSwiper = 0;
    new StorageService(starCitiesStorage).get().subscribe((res) => {
      this.StarCityList = res.data;
    });
  }
  // 创建广告
  public CreateAd() {
    // #ifdef MP-WEIXIN
    if (wx.createInterstitialAd) {
      interstitialAd = wx.createInterstitialAd({
        adUnitId: 'adunit-7c80ea03afc3f4f5',
      });
      interstitialAd.onLoad(() => {
      });
      interstitialAd.onError((err: any) => {
      });
      interstitialAd.onClose(() => {
      });
    }
    // #endif
  }
  // 显示广告
  public ShowAds() {
    // #ifdef MP-WEIXIN
    setTimeout(() => {
      if (interstitialAd) {
        interstitialAd.show().catch((err: any) => {
          console.error(err);
        });
      }
    }, 5000);
    // #endif
  }
  private MainSwiperChange(e: any) {
    const current = e.detail.current;
    this.CityImage = null;
    this.MainSwiper = current;
    this.StarCityData(current);
  }
  private SetToday() {
    const date = new Date();
    const month = date.getMonth() + 1;
    const day = date.getDate();
    const hours = date.getHours();
    this.Today = {
      month: month,
      date: day,
      hours: hours,
    };
  }
  private GetCityCode() {
    new AddressService().area().pipe(
        switchMap((area) => new StorageService(starCitiesStorage).get().pipe(
            map((result) => {
              const cities: any[] = [area];
              result.data.splice(0, 1);
              cities.push(...result.data);
              return cities;
            }),
            catchError(() => of([area])),
        )),
        switchMap((cities) => {
          starCitiesStorage.value = new StorageValue<Array<any>>(cities);
          return new StorageService(starCitiesStorage).set().pipe(map((result) => result.data));
        }),
    ).subscribe(
        (response) => {
          this.Address = response[0];
          this.Address.icon = true;
          this.StarCityList = response;
          this.isWeatherStorage(response[0].id);
        },
    );
  }
  private getRealTimeWeather(cityCode: string) {
    return realTimeWeather(cityCode).pipe(map((response) => {
      const RealTimeWeather: any = {
        Air: {
          Index: response.air,
          Level: response.air_level,
          Pm25: response.air_pm25,
          Tips: response.air_tips,
          Humidity: response.humidity,
          Pressure: response.pressure,
          Visibility: response.visibility,
        },
        Address: {
          Country: response.country,
          City: response.city,
          CityId: response.cityid,
        },
        Weather: {
          Type: response.wea,
          Icon: response.wea_img,
        },
        temperature: {
          current: response.tem,
          Max: response.tem1,
          Min: response.tem2,
        },
        Wind: {
          Type: response.win,
          Meter: response.win_meter,
          speed: response.win_speed,
        },
      };
      const V = this.ReturnWindSpeed(RealTimeWeather.Wind.speed);
      const T = RealTimeWeather.temperature.current;
      const RH = parseInt(RealTimeWeather.Air.Humidity);
      const AT = this.ReturnAT(T, V, RH);
      RealTimeWeather['temperature']['Somatosensory'] = AT;
      return RealTimeWeather;
    }));
  }
  private getWeekWeather(cityCode: string) {
    return weekWeather(cityCode).pipe(map((response) => {
      const OneWeekWeather: any = [];
      response = response.data;
      for (const key in response) {
        if (response.hasOwnProperty(key)) {
          const week = response[key].week.substr(response[key].week.length - 1, 1);
          const date = parseInt(response[key].day.split('日')[0]);
          const type = response[key].wea;
          const max = response[key].tem1;
          const min = response[key].tem2;
          const index = response[key].index;
          index[0]['title'] = '紫外线';
          index[1]['title'] = '运动';
          index[2]['title'] = '血糖';
          index[3]['title'] = '穿衣';
          index[4]['title'] = '洗车';
          index[5]['title'] = '空气污染';
          index[0]['icon'] = 'fa-sun-o';
          index[1]['icon'] = 'fa-child';
          index[2]['icon'] = 'fa-heartbeat';
          index[3]['icon'] = 'fa-male';
          index[4]['icon'] = 'fa-car';
          index[5]['icon'] = 'fa-envira';
          const hours = response[key].hours;
          for (const keychild in hours) {
            if (hours.hasOwnProperty(keychild)) {
              hours[keychild]['hours'] = hours[keychild]['hours'].split('时')[0];
              hours[keychild]['title'] = hours[keychild]['hours'] + ':00';
            }
          }
          OneWeekWeather[key] = {
            Date: {
              Week: week,
              Date: date,
            },
            Weather: {
              Type: type,
            },
            Temperature: {
              Max: max,
              Min: min,
            },
            Index: index,
            Hours: hours,
          };
        }
      }
      return OneWeekWeather;
    }));
  }
  private isWeatherStorage(cityCode: any) {
    zip(
        new StorageService(realTimeWeatherStorage(cityCode)).get().pipe(
            catchError((err) => this.getRealTimeWeather(cityCode).pipe(
                switchMap((res) => new StorageService(realTimeWeatherStorage(cityCode, res)).set()),
            )),
        ),
        new StorageService(weekWeatherStorage(cityCode)).get().pipe(
            catchError(() => this.getWeekWeather(cityCode).pipe(
                switchMap((weather) => new StorageService(
                    weekWeatherStorage(cityCode, weather),
                ).set()),
            )),
        ),
    ).pipe(
        finalize(() => this.StartupStatus = false),
    ).subscribe((response) => {
      this.realTimeWeather = response[0].data;
      this.OneWeekWeather = response[1].data;
    });
  }
  private ReturnAT(T: any, V: any, RH: any) {
    const e = (RH / 100) * 6.105 * Math.exp((17.27 * T) / (237.7 + T));
    const AT = (1.07 * T) + (0.2 * e) - (0.65 * V) - 2.7;
    return AT.toFixed(0);
  }
  private ReturnWindSpeed(WindLevel: any) {
    WindLevel = parseInt(WindLevel);
    switch (WindLevel) {
      case 0:
        return 0.1;
      case 1:
        return 0.9;
      case 2:
        return 2.45;
      case 3:
        return 4.4;
      case 4:
        return 6.7;
      case 5:
        return 9.35;
      case 6:
        return 12.3;
      case 7:
        return 15.5;
      case 8:
        return 18.95;
      case 9:
        return 22.6;
      case 10:
        return 26.45;
      default:
        return 0.1;
    }
  }
  private SwitchTemperatureStatus(e: any) {
    const status = e.currentTarget.dataset.id;
    this.TemperatureStatus = status;
  }
  private async StarCityData(current: any) {
    new StorageService(starCitiesStorage).get().subscribe((res) => {
      this.isWeatherStorage(res.data[current].id);
      this.Address = res.data[current];
    });
  }
  private ShowIndexDesc(desc: string) {
    uni.showToast({
      title: desc,
      icon: 'none',
    });
  }
  private more(key: number) {
    const starCityList: any = this.StarCityList;
    uni.navigateTo({
      url: './monthWeather/monthWeather?city=' + starCityList[key].id,
    });
  }
  private toSearch() {
    console.log(1111);
    uni.navigateTo({
      url: '/pages/search/search',
    });
  }
}
</script>
<style lang="stylus" scoped>
.page
  background #ffffff
  .status-bar
    width 100%
    position fixed
    top 0
    background #FFFFFF
    z-index 1
  .header
    width: 100%
    height: 80upx
    display: flex
    justify-content: flex-start
    align-items: center
    .icon
      width: 80upx;
      height: 80upx;
    .name
      font-size: 32upx;
      margin-left: 2vw;
      margin-right: 2vw;
      font-weight: bold;
      color: #9e9e9e;
    .current
      font-size: 32upx;
      font-weight: bold;
  .card
    width: 100%
    height: auto
    padding 0 25upx
    .header
      width: 100%;
      height: 80upx;
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 32upx;
      font-weight: bold;
      .address
        display: flex;
        align-items: center;
        justify-content: space-around
        .right
          margin-left: 2vw;
          font-weight: normal;
          overflow: hidden;
          text-overflow: ellipsis;
          white-space: nowrap;
          max-width: 45vw;
          font-size: 28upx;
    .main
      width: 100%;
      padding: 15upx
      box-shadow: 0 4upx 8upx rgba(0, 0, 0, 0.16), 0 4upx 8upx rgba(0, 0, 0, 0.23);
  .today-weather
    height: auto
    display: flex;
    flex-direction: column;
    background-size: 100%, 100%;
    .start
      display: flex;
      height: 15vw;
      .temperature
        display: flex;
        height: 15vw;
        flex-direction: column;
        align-items: center;
        justify-content: space-between;
        .status
          display: flex;
          width: 15vw;
          height: 40upx;
          align-items: center;
          justify-content: center;
          font-size: 24upx;
          view
            width: 7.5vw;
            height: 40upx;
            display: flex;
            align-items: center;
            justify-content: center;
            background: rgba(0, 0, 0, 0.16);
            color: #FFFFFF;
        .value
          width: 15vw;
          display: flex;
          align-items: center;
          justify-content: center;
          font-size: 60upx;
      .type
        height: 15vw;
        display: flex;
        flex-direction: column;
        align-items: flex-start;
        justify-content: space-between;
        margin-left: 2vw;
        .title
          font-size: 50upx;
        .temperature
          display: flex;
          flex-direction: row
          font-size: 24upx;
          align-items: center;
          view
            margin-right: 1vw;
    .end
      display: flex;
      font-size: 24upx;
      .item
        margin-right: 1vw;
  .today-air
    display: flex;
    align-items: center;
    justify-content: space-between;
    .item
      font-size: 24upx;
      display: flex;
      flex-direction: column;
      align-items: center;
      .title
        color: #9e9e9e;
      .content
        font-size: 28upx;
        color: black;
  .today-tips
    font-size: 28upx
  .hours
    width: 100%;
    height: 130upx;
    white-space: nowrap;
    line-height: 130upx;
    .item
      display: inline-block;
      width: 18vw;
      height: 130upx;
      text-align: center;
      vertical-align: middle;
      .title
        font-size: 24upx;
        font-weight: bold;
        color: #9E9E9E;
        height: 40upx;
        line-height: 40upx;
      .weather
        font-size: 28upx;
        font-weight: bold;
        height: 50upx;
        line-height: 50upx;
      .tem
        font-size: 24upx;
        font-weight: bold;
        height: 40upx;
        line-height: 40upx;
    .date
      width: 80upx;
      height: 80upx;
      line-height: 80upx;
      display: inline-block;
      vertical-align: middle;
      color: #9E9E9E;
      font-size: 32upx;
      font-weight: bold;
      background: rgba(0, 0, 0, 0.05);
  .week-weather
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    .item
      flex: 1;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      font-size: 28upx;
      font-weight: bold;
      view
        min-height: 50upx;
        display: flex;
        align-items: center;
        justify-content: center;
      .weather
        writing-mode: tb-rl;
        height: 160upx;
        white-space: nowrap;
        display: flex;
        justify-content: center;
        align-items: center;
        font-size: 24upx;
  .index
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    .item
      width: 28vw;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: space-around;
      font-size: 24upx;
      margin-top: 2vw;
      margin-bottom: 2vw;
      .icon
        width: 50upx;
        height: 50upx;
      .title
        color: #9E9E9E;
        height: 40upx;
        display: flex;
        justify-content: center;
        align-items: center;
      .level
        height: 40upx;
        display: flex;
        justify-content: center;
        align-items: center;
  .ad-bar
    // #ifdef MP-WEIXIN
    padding: 0 !important;
    // #endif
    // #ifdef MP-QQ
    padding: 25upx 0 !important;
    // #endif
    ad
      width: 100%;
  .copyright
    margin-top: 5vw;
    font-size: 32upx;
    color: #9e9e9e;
    margin-bottom: 100upx;
</style>
