<template>
    <div class="memberlogin" v-click-outside="closeDialog">
        <div class="menberCentral" @click="menberCentral">
                  <img class="showMenberCentral" src="/images/mobile/Mobile-index_01.png">
                    <div class="lang_flow" v-show="showMenberCentral" @click="memberCentral">
                        <div data-to="/account/memberInfo" class="ii">{{$t('Account.MemberInformation')}}</div>
                        <div data-to="/account/notification" class="ii">{{$t('Account.MyMessages')}}</div>
                        <div data-to="/order/List" class="ii">{{$t('Account.MyOrder')}}</div>
                        <div data-to="/account/deliveryAddress" class="ii">{{$t('Account.DeliveryAddress')}}</div>
                        <div data-to="/account/mycoupon" class="ii">{{$t('MyCoupon.MyCoupon')}}</div>
                        <div @click="logout">{{$t('Account.Logout')}}</div>
                    </div>
          </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Cookie from 'js-cookie';
@Component
export default class InsLangSwitch extends Vue {
  private showMenberCentral:boolean = false;

  closeDialog () {
    this.showMenberCentral = false;
  }
  menberCentral () {
    if (!this.$Storage.get('isLogin')) {
      window.location.href = '/account/login';
    } else {
      this.showMenberCentral = !this.showMenberCentral;
    }
  }
  memberCentral (e) {
    let target = e.target as HTMLElement;
    if (target.className === 'ii' && target.dataset.to) {
      this.$router.push({
        path: target.dataset.to
      });
    }
  }
  logout () {
    this.$Api.member.logout().then((result) => {
      if (result) this.$message('Message.SucceedInOperating');
      this.$router.push('/');
      this.Reload();
    });
  }
}
</script>
<style scoped lang="less">
  .menberCentral{
    padding-left: 1rem;
    padding-right: 1rem;
    float: left;
    height: 1.6rem;
    // border-right: 1px solid #e6e6e6;
    display: flex;
    align-items: center;
    justify-content: center;
    .lang_flow{
    position: absolute;
    top: 5rem;
    right: 0px;
    width: 100vw;
    background: #FFF;
    z-index: 99;
    >div{
      color:#000;
      font-size: 1.4rem;
      height: 4rem;
      display: flex;
      justify-content: center;
      align-items: center;
      border-bottom: 1px solid #ccc;
    }
    }
    img{
      width:1.6rem;
      margin: 0 auto;
      display: block;
    }
  }
</style>
