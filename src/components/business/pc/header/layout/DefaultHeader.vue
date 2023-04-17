<template>
<div class="header-layout"  v-cloak>
  <div class="headerBg" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="headerback"></div>
      <div class="headerTop fix">
          <div class="inner">

              <!-- 会员登陆开始 -->
              <InsLogin class="memberLogin"></InsLogin>
              <!-- 我的喜爱开始 -->
              <div class="cartTop">
                  <router-link to="/account/MyFavorite">
                          <i class="handle-icon fav-icon"></i>
                  </router-link>
              </div>
              <!-- 我的喜爱结束 -->
              <!-- 购物车开始 -->
              <Shopcart class="memberLogin"></Shopcart>
              <!-- 购物车结束 -->
              <!-- 切换语言开始 -->
              <!-- <CodeSelect/> -->
              <div class="langBox">
                  <InsLangSwitch></InsLangSwitch>
              </div>
              <!-- 切换语言结束 -->
          </div>
      </div>
      <div class="headerFooter fix">
        <!-- logo开始 -->
        <div class="logoBox">
            <a href="/"><img src="/images/pc/pcindex_09.png"></a>
        </div>
        <!-- logo结束 -->
        <!-- 导航栏开始 -->
        <Menu/>
        <!-- 导航栏结束 -->
        <!-- 搜索框开始 -->
        <div class="search-box">
          <input type="text" v-model="key" class="inputBox" />
          <span class="searchBtn" @click="searchFun(key)"></span>
        </div>
        <!--搜索框结束  -->
      </div>
  </div>
</div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component({
  components: {
    Menu: () =>
      import('@/components/business/pc/header/InsMenu.vue'),
    Shopcart: () =>
      import('@/components/business/pc/header/InsShoppingCart.vue'),
    InsLogin: () =>
      import('@/components/business/pc/header/InsLogin.vue'),
    InsLangSwitch: () =>
      import('@/components/business/pc/header/InsLangSwitch.vue'),
    CodeSelect: () =>
      import('@/components/business/pc/header/InsCodeSelect.vue')
  }
})
export default class DefaultHeader extends Vue {
  @Prop() private showInFixed!: boolean;

  private key: string = '';

  getMenu () {
    this.$Api.promotion
      .getMenu()
      .then(result => {
        this.$store.dispatch('setHeaderMenus', result.ReturnValue.HeaderMenus);
        this.$store.dispatch('setFooterMenus', result.ReturnValue.FooterMenus);
      })
      .catch(error => {
        console.log(error);
      });
  }

  searchFun (key) {
    this.$store.dispatch('setSearchKey', key);
    if (key !== '') {
      this.$router.push({
        path: '/product/search',
        name: 'productSearch',
        params: {
          key: key
        }
      });
    } else {
      this.$router.push({
        path: '/product/search/-'
      });
    }
  }
  get currentlang () {
    return this.$i18n.locale;
  }
  private changLange (lang) {
    this.$Api.member
      .setUILanguage(lang)
      .then(result => {
        this.$i18n.locale = lang;
        localStorage.setItem('locale', lang);
        this.Reload();
      })
      .catch(error => {
        console.log(error);
      });
  }

  created () {
    this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
  }

  mounted () {
    this.getMenu();
  }
}
</script>

<style scoped lang="less">
.showMenuYes{
  height:151px;
  transition:all 1s;
}
#header{
  z-index: 9999;
  top:0px;
  width: 100%;
  display: flex;
}
.headerBg{
   width: 100%;
   background:#fff;
   background-size: cover;
   display: block;
   box-shadow: 0 0 4px 0 #666666;
  position: relative;
  height: 100px;
  z-index: 10000;
}

.headerback{
  width: 100%;
  height: 26px;
  background: url('/images/pc/index_02.png') top center;
  position: absolute;
  top: 0;
  left: 0;
}
.headerTop{
    width: 1200px;
    margin: 0 auto;
    // padding-top: 10px;
    // height: 34px;
}
.headerTop .inner{
    float: right;
}
.search-box {
    border: 1px solid #dcd9d3;
    width: 250px;
    display: flex;
    float: right;
    align-items: center;
    // margin-right: 20px;
    box-sizing: border-box;
    margin-top: 10px;
    border-radius: 3px;
}
.searchBtn{
    width: 19px;
    height: 19px;
    display: inline-block;
    background: url('/images/pc/pcindex_03.png') no-repeat center center;
    background-size: cover;
    cursor: pointer;
    margin-right: 9px;
}
.search-box .inputBox {
    width: 100%;
    float: left;
    border:none;
    background: transparent;
    line-height: 38px;
    text-indent: 10px;
    padding: 0;
}
.search-box  .btn-send {
    position: absolute;
    right: 10px;
    top: 6px;
    line-height: 1;
    background: none;
    border: none;
    color: #555;
}
.search-box form {
    position: relative;
    font-size: 12px;
}
.search-box input {
    font-family: inherit;
    font-size: inherit;
    font-style: inherit;
    font-weight: inherit;
    outline: 0;
}
.memberLogin{
    display: flex;
    float: left;
    align-items: center;
    position: relative;
    // margin-right: 10px;
    width: 35px;
    height: 40px;
    box-shadow: 0 0 5px #e9e7e4;
    background-color: #fff;
    border-bottom-left-radius: 20px;
    border-bottom-right-radius: 20px;
    justify-content: center;
}
.cartTop{
    display: flex;
    float: left;
    align-items: center;
    position: relative;
    margin-right: 10px;
    margin-left: 10px;
    width: 35px;
    height: 40px;
    box-shadow: 0 0 5px #e9e7e4;
    background-color: #fff;
    border-bottom-left-radius: 20px;
    border-bottom-right-radius: 20px;
    justify-content: center;
}
.langBox{
    display: flex;
    float: left;
    align-items: center;
    position: relative;
    justify-content: center;
    margin-left: 0;
}
.langBox a{
   color:#323232;
   font-size: 14px;
    line-height: 32px;
    margin-right: 10px;
    width: 40px;
    display: inline-block;
    text-align: center;
}
.langBox a:nth-child(2){
    margin-right: 0px!important;
}
.langActive{
    background: #808080;
    color:#FFF!important;
}
.logoBox{
    // width: 100%;
    // text-align: center;
    // display: flex;
    // align-items: center;
    // justify-content: center;
    // padding-top: 40px;
    margin-bottom: -55px;
    z-index: 1000;
    margin-top: -30px;
    float: left;
    position: relative;
}
.logoBox a{
    display: flex;
}
.logoBox a img{
   width: 140px;
   display: block;
}

.fav-icon {
    background: url('/images/pc/pcindex_08.png') no-repeat center center;
    display: block;
    width: 20px;
    height: 19px;
    background-size: contain;
}

// new css
.header-layout {
 /deep/ .header_menu {
  //  width: 1200px;
  //  margin: 30px auto 10px;
  float: left;
  margin-top: 16px;
   > ul {

     > li {
      float: left;
      display: flex;
      align-items: center;
      position: initial;
      width: auto;
      padding: 0 20px;
      > a {
        width: 100%;
        font-size: 18px;
        color: #666666;
        display: block;
        text-align: center;
        font-weight: 500;
        // text-transform: uppercase;
        padding: 0;
        font-family: 'SourceHanSerifCN-Medium';
        padding-bottom: 18px;
      }

      &:hover{
        > a  {
          color: #957e52;
          // border-bottom: 3px solid #957e52;
          position: relative;
          &::after{
            content: '';
            width: 100%;
            height: 3px;
            background-color: #957e52;
            position: absolute;
            bottom: 0px;
            left: 0;
          }
        }
      }

      > ul {
        box-shadow: none;
        left: 50%;
        transform: translateX(-50%);
        width: 1200px;
        border-bottom-left-radius: 18px;
        border-bottom-right-radius: 18px;
        background-color: rgba(255, 255, 255, 0.95);
        top: 60px;
        padding: 30px 0;
        padding-left: 70px;
        box-sizing: border-box;
        // display: flex;
        // justify-content: center;

        >li {
          border: 0;
          background-color: transparent;
          float: none;
          width: auto;
          display: inline-block;
          padding: 0 25px;
          > a {
            font-size: 18px;
            color: #999999;
            display: block;
            text-align: center;
            font-weight: 500;
            padding: 0;
            // text-transform: uppercase;
            // padding: 10px 5px;
          }

          &:hover{
             > a {
              // background: @base_color;
              color: #957e52;
            }
          }
        }
      }
     }
   }
 }
}
.headerFooter{
  // display: flex;
  // justify-content: center;
  width: 1200px;
  margin: 0 auto;
  align-items: center;
  position: relative;
}
.ENG{
  .headerFooter{
    // display: flex;
  }
  /deep/ .header_menu{
    margin-top: 10px;
    width: 810px;
    > ul {
          // padding-bottom: 9px;
      > li {
      width: 12.5%;
      padding: 0 6px;
      // height: 36px;
      box-sizing: border-box;
      // display: inline-block;

      > a{
        font-family: 'Domine-SemiBold' !important;
        // height: 36px;
        font-size: 16px;
        padding-bottom: 14px;
        height: 50px;
        display: flex;
        align-items: center;
        justify-content: center;
      }
    }
    }
  }
}
</style>
