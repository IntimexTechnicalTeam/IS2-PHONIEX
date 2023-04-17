<template>
 <div id="footer">
  <div class="footerW">
    <div class="footer-box">
          <!-- <p><img src="/images/mobile/logo_white.png"></p>
          <div class="contactBox">
              <div class="w50">
                  <p>whatsapp&nbsp;{{$t('home.Order')}}</p>
                  <p>6289 1789</p>
              </div>
              <div class="w50">
                  <p>{{$t('home.TelSearch')}}</p>
                  <p>6289 1789</p>
              </div>
          </div> -->
          <div class="footerNav">
                  <ul>
              <li
                v-for="(item, index) in $store.state.footerMenus"
                :key="index"
              >
                <a
                  href="javascript:;"
                  v-if="item.Type === 0"
                  @click="toUrl(item)"
                >
                  {{ item.Name }}
                </a>
                <router-link v-else :to="To(item)" slot="title">
                  {{ item.Name }}
                </router-link>
                <!-- <ul>
                  <li v-for="(c, index2) in item.Childs" :key="index2">
                    <router-link :to="To(c)">{{ c.Name }}</router-link>
                  </li>
                </ul> -->
              </li>
            </ul>
              </div>
          <!-- <div class="footerAccept">
            <p>{{$t('home.Weaccept')}}<img src="/images/mobile/pcindex_15.png"></p>
          </div> -->

    </div>
  </div>

    <div class="footerCpy">
      <p>Copyright {{currentYear}} © Phoenix Creating Company Ltd<br>Powered by Eventizer
      <a href="https://eventizer.hk/" target="_blank">
        <img src="/images/mobile/footerlogo.png">
      </a>
      </p>
    </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';

@Component({
  components: {}
})
export default class InsFooter extends Vue {
  currentYear: number = 0;
  clickIndex: number = 0;
  footerMenus: any[] = [];
  showMeun (item, index) {
    $('.sub' + index).slideToggle();
  }
  closeSlideMenu (n) {
    this.$store.dispatch('isShowMenu', false);
  }
  // goToTop () {
  //   let sTop = document.documentElement.scrollTop;
  //   let times = setInterval(() => {
  //     sTop -= 50;
  //     if (sTop <= 0) {
  //       document.documentElement.scrollTop = 0;
  //       clearInterval(times);
  //     } else {
  //       document.documentElement.scrollTop = sTop;
  //     }
  //   }, 1);
  // }
  toUrl (n) {
    console.log(n, 'toUrl');
    if (n.Type < 0) {
      return;
    }

    if (!n.IsNewWin && n.Url) {
      window.location.href = n.Url;
    } else if (n.IsNewWin && n.Url) {
      window.open(n.Url);
    }
  }
  To (n) {
    let url = '';
    if (n.Type === -1) {
      url = '';
    } else if (n.Type === 1 && n.IsAnchor === false) {
      url = '/cms/catDetail/' + n.Value.Id;
    } else if (n.Type === 1 && n.IsAnchor === true) {
      url = '/CMS/catDetail/' + n.ParentId + '#' + n.Value.Id;
    } else if (n.Type === 2) {
      url = '/CMS/content/' + n.Value.Id;
    } else if (n.Type === 3) {
      url = '/RegNPay/Form/' + n.Value.Id;
    } else if (n.Type === 4 && !this.$store.state.catMenuType) {
      url = '/product/cat/' + n.Value.Id;
    } else if (n.Type === 4 && this.$store.state.catMenuType) {
      url =
        '/product/search/-?catalogs=' +
        JSON.stringify([parseInt(n.Value.Id)]) +
        '&type=0';
    } else if (n.Type === 5) {
      url =
        '/product/search/-?attrs=' +
        JSON.stringify([{ Id: parseInt(n.Value.Id), Vals: [] }]) +
        '&type=0';
    } else {
      url =
        '/product/search/-?attrs=' +
        JSON.stringify([
          { Id: parseInt(n.ParentId), Vals: [parseInt(n.Value.Id)] }
        ]) +
        '&type=0';
    }
    return url;
    // return n.Type === 1 ? '/cms/catDetail/' + n.Value.Id : n.Type === 2 ? '/CMS/content/' + n.Value.Id : n.Type === 3 ? '/RegNPay/Form/' + n.Value.Id : n.Type === 4 && !this.$store.state.catMenuType ? '/product/cat/' + n.Value.Id : n.Type === 4 && this.$store.state.catMenuType ? '/product/search/-?catalogs=' + JSON.stringify([parseInt(n.Value.Id)]) + '&type=0' : n.Type === 5 ? '/product/search/-?attrs=' + JSON.stringify([{ Id: parseInt(n.Value.Id), Vals: [] }]) + '&type=0' : '/product/search/-?attrs=' + JSON.stringify([{ Id: parseInt(n.ParentId), Vals: [parseInt(n.Value.Id)] }]) + '&type=0';
  }
  getMenu () {
    this.$Api.promotion.getMenu().then((result) => {
      this.footerMenus = result.ReturnValue.FooterMenus;
    });
  }
  created () {
    var date = new Date();
    this.currentYear = date.getFullYear();
    this.getMenu();
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    $('.submeunMain').hide();
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
.submeunMain{
  display: none;
}
.SubMeun0{
  display: none;
}
.SubMeun1{
  display: none;
}
#footer{
  width: 100%;
  // background: url('/images/mobile/MobileIndex_04.jpg') no-repeat center center;
  background-size: cover;
  display: inline-block;
  // position: relative;
  margin-top: 1rem;
  border-top: 1px solid #b59e72;
  .footerW{
      position: relative;

    &::after{
      content: '';
      width: 100%;
      height: 100%;
      background: url('/images/mobile/footerback.png') no-repeat center center;
      background-size: 102% auto;
      position: absolute;
      top: 0;
      left: 0;
    }
  }
  .footer-box{
    width: 90%;
    margin: 0 auto;

    padding-top: 0;
    padding-bottom: 0;

    .footerAccept{
      width: 100%;
      >p{
        font-size: 1.4rem;
        color:#fff;
        display: flex;
        align-items: center;
        justify-content: center;
        img{
          display: block;
          margin-left: 1rem;
        }
      }
    }
    .footerNav{
      width: 100%;
      margin: 0 auto;
      margin-top: 1rem;
      margin-bottom: 2rem;
      position: relative;
      z-index: 10;
      >ul>li{
        width: 100%;
        display: block;
        text-align: center;
        position: relative;
        border-bottom: 1px solid #e6e6e6;
        padding: 1rem 0;
        &:last-child{
          border-bottom: none;
        }
        >ul{
          position: relative;
        }
        >ul>li{
        width: 100%;
        display: inline-block;
        background: transparent;
        border-radius: 10px;
        height: 3.5rem;
        line-height: 3.5rem;
        text-align: center;
        margin-bottom: .5rem;
          >a{
            font-size: 1.6rem;
            color:#fff;
            font-weight: 500;
            text-decoration: none;
          }
        }
        >a{
          font-size: 1.4rem;
          color:#666666;
          font-weight: 500;
          text-decoration: none;
          position: relative;
          display: block;
          // background: #ffffff;
          // border-radius: 5px;
          // margin-bottom: .5rem;
          i{
               background: url('/Images/mobile/downicon.png') no-repeat center center;
               background-size: contain;
               width: 1.2rem;
               height: 1.2rem;
               display: inline-block;
               position: absolute;
               right: 1rem;
               top: 1.4rem;
          }
        }
      }
    }
    .contactBox{
    width: 100%;
    display: inline-block;
    margin-top: 2rem;
      .w50{
        width: 50%;
        float: left;
        >p:nth-child(1){
          font-size: 1.4rem;
          text-align: center;
          color: #FFF;
          padding-bottom: .5rem;
        }
        >p:nth-child(2){
            font-size:2.2rem;
            text-align: center;
            color: #FFF;
        }
      }
    }
    >p{
      width:70%;
      margin: 0 auto;
      img{
        width: 100%;
      }
    }
  }
  .footerCpy{
      width: 100%;
      display: inline-block;
      padding-top: 1rem;
      padding-bottom: 1rem;
      background-color: #f5f5f5;
      >p{
        color:#999999;
        font-size: 1.1rem;
        text-align: center;
        img{
          width: 3.5rem;
          display: inline-block;
          margin-left: 1rem;
          vertical-align: middle;
        }
      }
    }
}
</style>
