<template>
    <div class="langSwitch">
        <!-- <p @click="changeLang('S')" :class="{'active': $Storage.get('locale') === 'S'}">简</p> | -->
        <p @click="changeLang('E')" :class="{'active': $Storage.get('locale') === 'E'}">Eng</p>
        <p @click="changeLang('C')" :class="{'active': $Storage.get('locale') === 'C'}">繁</p>
      <!-- <p @click="changeLang(lang.value)" v-for="(lang,index) in FrontE.langList" :key="index" :class="{'active': $Storage.get('locale') === lang.value}">{{lang.name}}</p> -->
      <!-- <select  v-model="currentlang">
        <option :value="lang.value" v-for="(lang,index) in FrontE.langList" :key="index">{{lang.name}}</option>
      </select> -->
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Cookie from 'js-cookie';
@Component
export default class InsLangSwitch extends Vue {
  get currentlang () {
    return this.$i18n.locale;
  }
  set currentlang (val) {
    this.changeLang(val);
  }
  changeLang (lang) {
    this.$Api.member.setUILanguage(lang).then((result) => {
      this.$i18n.locale = lang;
      localStorage.setItem('locale', lang);
      this.Reload();
    }).catch((error) => {
      console.log(error);
    });
 }
}
</script>
<style scoped lang="less">
.langSwitch {
    p {

      display: flex;
      float: left;
      align-items: center;
      position: relative;
      margin-left: 10px;
      width: 35px;
      height: 40px;
      box-shadow: 0 0 5px #e9e7e4;
      background-color: #fff;
      border-bottom-left-radius: 20px;
      border-bottom-right-radius: 20px;
      justify-content: center;
      color: #957e52;
      font-family: 'SourceHanSerifCN-Medium';
      font-size: 14px;
    }
    .active{
      background: #957e52;
      color: #fff!important;
    }
    select{
      width: 100%;
      background: transparent url('/images/mobile/arrow-down-back.png')  80% 50% no-repeat;
      background-size: 1rem;
      border:none;
      box-sizing: border-box;
      text-align: center;
      color:#666666;
      appearance: none;
      -moz-appearance: none;
      -webkit-appearance: none;
      padding-left: .8rem;
      font-size: 1.4rem;
      outline: none;
      option{
        color:#666666;
        outline: none;
      }
    }
}
</style>
