<template>
  <div class="MembercenterMobile">
    <accountHeader />
    <div class="MemberInfoNav fix">
      <ul class="fix">
        <div @click="openlink('/account/memberInfo')">
        <li :class="activeClass == 1?'activeInfo':''" >
          <a>{{ $t("MemberInfo.MemberInfoTitle") }}</a>
        </li>
        </div>
        <div @click="openlink('/account/modifyPassword')">
        <li :class="activeClass == 2?'activeInfo':''" >
          <a>{{ $t("MemberInfo.ModifyPassword") }}</a>
        </li>
        </div>
        <div @click="openlink('/account/deliveryAddress')">
        <li :class="activeClass == 3?'activeInfo':''" >
          <a>{{ $t("Account.DeliveryAddress") }}</a>
        </li>
        </div>
      </ul>
    </div>
    <router-view></router-view>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { MemberResult } from '@/model/memberResult';
import sdk from '@/sdk/InstoreSdk';
import Cookie from 'js-cookie';
import lang from '@/lang';
import storage from '@/sdk/common/Storage';
@Component({
  components: {
    accountHeader: () => import('@/components/hkTasteBusiness/mobile/account/accountHeader.vue')
  }
})
export default class InsMenberCenter extends Vue {
    activeClass: any = 0;
    openlink(link) {
    this.$router.push({ path: link });
  }
  isActive() {
    var url = this.$route.path;
    var reg = RegExp(/memberInfo/);
    if (reg.test(url)) {
      this.activeClass = 1;
    } else if (url.indexOf('modifyPassword') !== -1) {
      this.activeClass = 2;
    } else if (url.indexOf('deliveryAddress') !== -1) {
      this.activeClass = 3;
    }
    console.log(this.activeClass);
  }
  updated() {
    this.isActive();
  }
  mounted() {
    this.isActive();
  }
}
</script>
<style lang="less" scoped>
#container {
  width: 100%;
  display: inline-block;
}
.MemberInfoMain {
  width: 1200px;
  margin: 0 auto;
  margin-top: 1rem;
  padding: 2.5%;
}
.MemberInfoMain .el-tabs__nav {
  height: 50px;
  line-height: 50px;
}
.MemberInfoMain .el-tabs__content {
  text-align: left;
}
.MemberInfoMain .el-input__icon {
  font-size: 16px;
}
.MemberInfoMain .el-form-item__content,
.MemberInfoMain .el-form-item__label-wrap {
  margin-left: 0px !important;
}
.MemberInfoMain .el-button {
  background: #333333;
  border: none;
  display: block;
  border-radius: 20px;
  width: 60%;
  float: left;
  margin-left: 25%;
}
.MembercenterMobile .MemberInfoNav{
    width: 90%;
    margin: 0 auto;
    margin-top: 3rem;
    display: block;
    background: #fff;

}
.MembercenterMobile .MemberInfoNav ul{
  width:100%;
  display: block;
  border: 1px solid #b59e72;
  border-radius: 3px;
  box-sizing: border-box;
}
.MembercenterMobile .MemberInfoNav li{
  float: left;
  list-style: none;
  width:33.3%;
  box-sizing: border-box;
}
.MembercenterMobile .MemberInfoNav div:nth-child(2) li{
    border-left: 1px solid #b59e72;
    border-right: 1px solid #b59e72;
  }
.MembercenterMobile .MemberInfoNav li a{
    text-align: center;
    text-decoration: none;
    display: block;
    font-size: 1.3rem;
    padding: 0.7rem 1rem;
    word-break: break-word;
    color: #b59e72;
}
.MembercenterMobile .activeInfo{
  background: #b59e72!important;
}
.MembercenterMobile .activeInfo a{
  color:#fff!important;
}
</style>
