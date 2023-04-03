<template>
 <div class="accountHeader" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="memberBg">
        <div class="innerBox">
            <p class="memberCenterTitle">{{$t('Account.MemberCenter')}}</p>
            <div class="leftside">
              <p>{{MemberName}}</p>
              <div class="leftnav">
                <router-link to="/account/memberInfo" class="iconBg"><div class="innerStyle"><span>{{$t('Account.MemberInformation')}}</span></div></router-link>
                <router-link to="/order/List" class="iconBg" ><div class="innerStyle"><span>{{$t('Account.MyOrder')}}</span></div></router-link>
                <router-link to="/account/notification" class="iconBg"><div class="innerStyle"><span>{{$t('Account.MyMessages')}}</span></div></router-link>
                <router-link to="/account/myFavorite" class="iconBg"  ><div class="innerStyle"><span>{{$t('Account.MyFavorite')}}</span></div></router-link>
                <router-link to="/account/myCoupon" class="iconBg" ><div class="innerStyle"><span>{{$t('MyCoupon.MyCoupon')}}</span></div></router-link>
              </div>
            </div>
        </div>
    </div>
 </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
import sdk from '@/sdk/InstoreSdk';
@Component
export default class accountHeader extends Vue {
   MemberName:string='';
   TotalPoints:string='';
   // 获取会员信息
   getMemberInfo () {
     let _this = this;
     sdk.api.member.getProfile().then(
       function (data) {
         console.log(data, 'datadata');
         _this.MemberName = data.FirstName + '  ' + data.LastName;
       }
     );
   }
   // 获取会员总积分
   getMemberTotal () {
     this.$Api.pointsApi.GetMemberTotalPointsInfo().then((result) => {
       this.TotalPoints = result.data.TotalPoints;
     });
   }
   get currentlang () {
     return this.$i18n.locale;
   }
   mounted () {
     this.getMemberInfo();
     this.getMemberTotal();
   }
}
</script>

<style scoped lang="less">
/*頁面中間目錄*/
ul,li{
    list-style: none;
}
.clear{
    clear: both;
}

.iconBg{
    border:1px solid #fff;
    background-size: contain;
    box-sizing: border-box;
    float: left;
    width: 224px;
    height: 45px;
    text-align: left;

    // padding: 10px;
    border-radius: 3px;
    overflow: hidden;
    .innerStyle{
      background: transparent;
      display: block;
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #fff;
      position: relative;
      &::after{
        content: '';
        width: 7px;
        height: 14px;
        background: url('/images/pc/PersonalCenterarrow.png') no-repeat center right;
        position: absolute;
        top: 15px;
        right: 9px;
      }
    }
    &:hover{
      transform: translateY(-3px);
    }
  span{
    width: 100%;
    display: inline-block;

    color:#fff;
    font-size: 18px;
    font-weight: 500;
    // text-transform:uppercase;
    //  padding: 10px;
    padding-left: 10px;
  }
  strong{
    width: 100%;
    display: inline-block;
    color: #262626;
    font-size: 24px;
    font-weight: 600;
  }
  b{
    width: 100%;
    display: inline-block;
    color: #262626;
    font-size: 14px;
    font-weight: 600;
  }
}
.router-link-active{
    border:1px solid #fff;
    box-sizing: border-box;
    background-size: contain;
    float: left;
    width: 224px!important;
    height: 45px!important;
    text-align: left;
    // padding: 10px;
    border-radius: 3px;
    overflow: hidden;
    .innerStyle{
      background: #fff !important;
      display: block;
      width: 100%;
      height: 100%;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #b59e72;
      position: relative;
      &::after{
        content: '';
        width: 7px;
        height: 14px;
        background: url('/images/pc/PersonalCenterarrow_hover.png') no-repeat center right;
        position: absolute;
        top: 15px;
        right: 9px;
      }
    }
   span{
    font-weight: 600!important;
    color: #b59e72;
    padding-left: 10px;
  }
}
.accountHeader{
    width: 100%;
    display: flex;
    .memberBg{
      background: url('/images/pc/Personal_Centerback.jpg') no-repeat center center;
      background-size: cover;
      box-sizing: border-box;
      display: inline-block;
      width: 100%;
      padding-top: 30px;
      padding-bottom: 30px;
      .innerBox{
        width: 1200px;
        margin: 0 auto;
        .memberCenterTitle{
          // font-weight: 700;
          font-family: 'SourceHanSerifCN-Bold';
          font-size: 22px;
          text-align: center;
          margin: 0 auto;
          margin-top: 30px;
          line-height: 50px;
          color:#FFF;
          // text-transform:uppercase;
          background: url('/images/pc/PersonalCenterbox.png') no-repeat center center;
          width: 254px;
          height: 50px;

        }
        .leftside{
          width: 100%;
          padding-top: 30px;
          p{
            font-size: 20px;
            color:#FFF;
            margin-bottom: 30px;
            text-align: center;
          }
          a{
            display: inline-block;
            margin-right: 15px;
            &:last-child{
              margin-right: 0px!important;
            }
            img{
              width: 100%;
            }
          }
          .leftnav{
            display: flex;
            justify-content: center;
            align-items: center;
          }
        }
        .rightside{
          width: 10%;
          float: left;
          text-align: right;
          img{
               display: inline-block;
          }
        }

      }
    }
}
.ENG{
  .iconBg span{
    font-family: 'Domine-Bold' !important;
    font-size: 18px;
  }
  .memberBg .innerBox .memberCenterTitle{
    font-family: 'Domine-Bold' !important;
    font-size: 20px;
  }
}
</style>
