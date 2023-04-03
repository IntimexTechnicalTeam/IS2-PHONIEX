<template>
    <div :class="{'ENG' : $Storage.get('locale') === 'E'}">
        <!-- <div class="header_logo" v-if="!this.FrontE.slideMenu.Embedded">
            <i class="el-icon-close" @click="closeSlideMenu"></i>
        </div> -->
        <!-- <div class="searchBox">
            <input type="text" v-model="searchKey" />
            <span class="search_btn"  @click="searchFun(searchKey)"><img src="/images/mobile/searchbtn.png"></span>
        </div> -->
        <div class="menu_top">
            <ins-fav />
            <ins-lang-switch class="headerLang" />
        </div>
        <div id="menu" >
            <Menu :backColor="'@base_color'" :textColor="'#fff'" :uniqueOpened="true" />
        </div>
        <!-- <div class="menu_footer">
            <div class="innerShare">
                <a href="https://www.facebook.com/hktastefood/" class="nav" target="_blank"><img src="/images/mobile/facebook.png"/></a>
                <a href="https://www.facebook.com/hktastefood/" class="nav" target="_blank"><img src="/images/mobile/ig.png"/></a>
                <a href="https://www.youtube.com/embed/videoseries?list=PLeU-XfKN4KcjVolI4daTvRI2oNOSLCILM"  class="nav" target="_blank"><img src="/images/mobile/youtube.png" /></a>
            </div>
        </div> -->
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';

@Component({
  components: {
    InsLogo: () => import('@/components/base/mobile/InsLogo.vue'),
    Menu: () => import('@/components/business/mobile/header/InsElMenu.vue'),
    InsLangSwitch: () => import('@/components/business/mobile/header/InsLangSwitch.vue'),
    InsFav: () => import('@/components/business/mobile/header/InsFav.vue')
  }
})
export default class InsMenuLayout extends Vue {
  showMemNav: boolean = false;
  searchKey: string = '';

  handleOpen (key, keyPath) {
    console.log(key, keyPath);
  }
  handleClose (key, keyPath) {
    console.log(key, keyPath);
  }

  closeSlideMenu () {
    this.$store.dispatch('isShowMenu', false);
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
      this.$store.dispatch('isShowMenu', !this.$store.state.isShowMenu);
    } else {
      this.$router.push({
        path: '/product/search/-'
      });
      this.$store.dispatch('isShowMenu', !this.$store.state.isShowMenu);
    }
  }

  get user () {
    return this.$store.state.user;
  }

  get isLogin () {
    return this.$store.state.isLogin;
  }
}
</script>

<style lang="less">
.sidebar-container {
    background-color: #fff !important;
}
.menu_footer span{
    display: flex;
    float: left;
    border:1px solid #eee;
    padding-top: .5rem;
    padding-bottom: .5rem;
    padding-left: 2rem;
    padding-right: 2rem;
    margin-right: 1rem;
    font-size: 1.4rem;
}
.innerShare{
    width: 90%;
    margin: 0 auto;
    display: flex;
    margin-top: 2rem;
    padding-bottom: 5rem;
    align-items: center;
    justify-content: center;
}
.innerShare a {
    display: inline-block;
    margin-right: 1rem;
    vertical-align: middle;
}
.innerShare a img {
    width: 3rem;
}
.searchBox{
    width: 90%;
    height: 4rem;
    margin: 0 auto;
    margin-top: 3rem;
    position: relative;
    overflow: hidden;
    margin-bottom: 2rem;
    border:1px solid #666666;
    input{
        width: calc(100% - 10px);
        height: 4rem;
        margin: 0 auto;
        text-indent: 10px;
        border:none;
    }
    .search_btn{
        position: absolute;
        right: 0px;
        top:0rem;
        width: 4rem;
        height: 4rem;
        background: #666666;
        display: flex;
        align-items: center;
        justify-content: center;
        img{
            width: 50%;
            margin: 0 auto;
            display: block;
        }
    }
}
#menu {
    .el-submenu__icon-arrow {
        display: none;
    }

    .el-submenu__title {
        color:#666666;
                 background-color: #fff !important;
                 background-size: 100% 100%;
                 display:block;
                 width: 100%;
                padding: 0 !important;
                height: auto;
                line-height: normal;
                 margin: 0 auto;
                //  border:1px solid #666666;
                 font-weight: 500;
                 position: relative;
                    // border-left: 1px solid #cec0a6;
                    // border-right: 1px solid #cec0a6;
                    box-sizing: border-box;
                //  &::before{
                //     content: '';
                //     width: 100%;
                //     height: 2rem;
                //     background: url(/images/pc/backline_09.png) no-repeat bottom;
                //     position: absolute;
                //     background-size: contain;
                //     top: -1px;
                //     left: 50%;
                //     transform: translateX(-50%) rotate(180deg);
                //  }
                //  &::after{
                //     content: '';
                //     width: 100%;
                //     height: 2rem;
                //     background: url(/images/pc/backline_09.png) no-repeat bottom;
                //     position: absolute;
                //     background-size: contain;
                //     bottom: -1px;
                //     left: 50%;
                //     transform: translateX(-50%);
                //  }
        .name{
            color:#666666;
                     display: block;
                     width: 100%;
                     font-weight: 500;
                    background-color: #f5f5f5 !important;
                    line-height: 3.4rem;
                    font-size: 1.5rem;
                    line-height: 3.4rem;
                    letter-spacing: 2px;
        }
    }

    > .el-menu {
        width: 90%;
        margin: 0 auto;
        background-color: transparent;
        border: 0;
        margin-bottom: 1rem;
        margin-top: 1rem;
        .el-submenu__icon-arrow {
            display: block;
            font-size: 1.6rem;
            color: #666666;
        }

        > li {
            height: auto;
            line-height: unset;
            text-align: center;
            margin-bottom: 1.5rem;
            border-left: 1px solid #cec0a6;
            border-right: 1px solid #cec0a6;
            position: relative;
            padding: 1rem !important;
            box-sizing: border-box;
            &::before{
                content: '';
                width: 100%;
                height: 2rem;
                background: url(/images/pc/backline_09.png) no-repeat bottom;
                position: absolute;
                background-size: contain;
                top: -1px;
                left: 50%;
                transform: translateX(-50%) rotate(180deg);
                z-index: 1;
            }
            &::after{
                content: '';
                width: 100%;
                height: 2rem;
                background: url(/images/pc/backline_09.png) no-repeat bottom;
                position: absolute;
                background-size: contain;
                bottom: -1px;
                left: 50%;
                transform: translateX(-50%);
                z-index: 1;
            }
             >a {
                 color:#666666;
                //  background: #f5f5f5;
                 background-size: 100% 100%;
                 display:block;
                 width: 100%;

                 margin: 0 auto;
                //  border:1px solid #666666;
                 font-weight: 500;
                 box-sizing: border-box;

                 b{
                     color:#666666;
                     display: block;
                     width: 100%;
                     font-weight: 500;
                    font-size: 1.5rem;
                    background-color: #f5f5f5;
                    line-height: 3.4rem;
                    letter-spacing: 2px;
                 }
            }

            a {
                text-decoration: none;
            }
            .el-menu{
                > li{
                    a{
                        b{
                            color:#fff;
                            display: block;
                            width: 100%;
                            font-weight: 500;
                            font-size: 1.5rem;
                            line-height: 3.4rem;
                            letter-spacing: 2px;
                        }
                    }
                }
            }
        }
        .el-submenu .el-menu{
                background-color: transparent;
            }
            .el-submenu.is-opened>.el-submenu__title .el-submenu__icon-arrow{
                margin-top: -12px;
            }
        // li {
        //     line-height: unset;
        //     // padding: 0 !important;
        //     min-width: unset;
        // }
    }
}
.ENG{
    #menu{
        > .el-menu > li > a > b{
            letter-spacing: 0;
            font-family: 'Domine-Bold' !important;
        }
        .el-submenu__title .name{
            letter-spacing: 0;
            font-family: 'Domine-Bold' !important;
        }
        .el-menu > li .el-menu > li a b{
            font-size: 1.2rem;
            line-height: 2rem;
            letter-spacing: 0;
        }
        .el-submenu .el-menu-item{
            padding: 0 !important;
            width: 100%;
            display: block;
            height: auto;
            line-height: normal;
            white-space:normal;
            margin-bottom: 0.5rem;
        }
    }
}
#menu .is-opened{
    background: url('/images/mobile/index_115.png') center center !important;
    background-size: contain !important;
}
#menu .is-opened > .el-submenu__title{
    background: transparent!important;
    color:#fff!important;
    .name{
        color:#fff!important;
        background-color: transparent !important;
    }
}
#menu .is-opened > .el-submenu__title .el-submenu__icon-arrow{
    color:#fff!important;

}
</style>

<style scoped lang="less">
.header_logo {
    height: 7rem;
    position: relative;
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 1rem 0 1.5rem;
    border-bottom: 4px solid #acbd30;
    background-color: #fff;

    .el-icon-close {
        color: #777777;
        font-size: 2.8rem;
    }

    a {
        width: 12rem;
    }

    .slide-menu {
        cursor: pointer;
    }
}

/deep/ .langSwitch {
    font-size: 1.5rem;
    color: #106919;
    text-align: center;
    p {
        font-size: 1.5rem;
        display: block;
        color: #fff;
        // margin: 0 0.8rem;

        &.active {
            color: #fff;
            background-color: #b59e72;
            // font-weight: bold;
        }
    }
}
.menu_top{
    text-align: right;
    width: 100%;
    display: flex;
    height: 4rem;
    justify-content: right;
    padding-right: 1.5rem;
    box-sizing: border-box;
}
</style>
