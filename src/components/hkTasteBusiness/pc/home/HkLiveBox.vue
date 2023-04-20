<template>
  <div class="indexHotVideo" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="TitleBg">
      <div class="line"></div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{$t('regnpay.ContactUs')}}</h2> -->
        <img src="/images/pc/M-indexC_14.png" alt="">
      </div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
          <img src="/images/pc/Mindex_16.png" alt="">
        </div>
    </div>
    <div class="top_ContactUs">
      <p v-html="UsContent.Body"></p>
    </div>
    <div class="HotVideoMain fix">
      <div class="leftVideo">
        <p v-html="videoContent.Body"></p>
      </div>
      <div class="rightVideo"><p v-html="fbContent.Body"></p></div>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class PkLiveBox extends Vue {
  videoContent:string='';
  fbContent:string='';
  UsContent:string='';
  getVideoContent () {
    this.$Api.cms.getContentByDevice({ Key: 'fb', IsMobile: false }).then(result => {
      this.videoContent = result.CMS;
    });
  }
  getFbContent () {
    this.$Api.cms.getContentByDevice({ Key: 'Instagram', IsMobile: false }).then(result => {
      this.fbContent = result.CMS;
    });
  }
  gettopContactUs () {
    this.$Api.cms.getContentByDevice({ Key: 'ContactUshome', IsMobile: false }).then(result => {
      this.UsContent = result.CMS;
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  created () {
    this.getVideoContent();
    this.getFbContent();
    this.gettopContactUs();
  }
}
</script>
<style  lang="less">
.HotVideoMain .leftVideo iframe{
  width: 500px;
  height: 600px;
  border-radius: 5px;
  border: 1px solid #edecf1;
  box-sizing: border-box;
}

.HotVideoMain .rightVideo iframe{
  width: 500px;
  height: 600px;
  border-radius: 5px;
  border: 1px solid #edecf1;
  box-sizing: border-box;
}

</style>
<style scoped lang="less">
.TitleBg{
  position: relative;
  .innerBox{
    position: absolute;
    left: 50%;
    transform: translate(-50%,-50%);
    top: 50%;
    img{
      height: 80px;
      display: block;
    }
    // &::after{
    //   content: '';
    //   width: 230px;
    //   height: 80px;
    //   background: url(/images/pc/index_24.png) no-repeat center center;
    //   position: absolute;
    //   left: 50%;
    //   transform: translateX(-50%);
    //   top: -16px;
    // }
    h2{
      font-size: 30px;
      color: #fff;
      text-align: center;
      font-family: 'SourceHanSerifCN-Bold';
      position: relative;
      z-index: 10;
      letter-spacing: 20px;
      padding-left: 20px;
    }
  }
  .line{
    width: 100%;
    height: 19px;
    background: url(/images/pc/index_27.png) center center;
  }
}
.indexHotVideo{
    width: 100%;
    padding-top: 80px;
    // padding-bottom: 80px;
}
.top_ContactUs{
  width: 1200px;
  margin: 0 auto;
  padding: 60px;
  padding-bottom: 0;
  box-sizing: border-box;
  /deep/ .ContactUs_table{
    width: 100%;
    tr{
      td{
        h2{
          font-family: 'SourceHanSerifCN-Bold';
          font-size: 40px;
          margin-bottom: 34px;
          color: #b59e72;
        }
        p{
          font-size: 20px;
          color: #4e4e4e;
          // margin-bottom: 32px;
          display: flex;
          align-items: center;
          img{
            margin-right: 10px;
          }
        }
      }
      .ContactUs_table_td{
        width: 530px;
        height: 180px;
        position: relative;
        border-left: 1px solid #cec0a6;
        border-right: 1px solid #cec0a6;
        display: block;
        margin-left: auto;
        box-sizing: border-box;
        margin-top: 34px;
        &::after{
              content: '';
              width: 530px;
              height: 25px;
              background: url('/images/pc/backline_13.png') no-repeat bottom;
              position: absolute;
              bottom: -1px;
              left: 50%;
              transform: translateX(-50%);
            }
            &::before{
              content: '';
              width: 530px;
              height: 25px;
              background: url('/images/pc/backline_13.png') no-repeat bottom;
              position: absolute;
              top: -1px;
              left: 50%;
              transform: translateX(-50%) rotate(180deg);
            }
        p{
          color: #fff;
          font-size: 24px;
          font-family: 'SourceHanSerifCN-Bold';
          line-height: 40px;
          text-align: center;
          justify-content: center;
        }
        .ContactUs_table_back{
          width: 500px;
          height: 150px;
          text-align: center;
          background: url('/images/pc/index_115.png') center center;
          display: block;
          margin: 14px;
          padding-top: 14px;
          box-sizing: border-box;
        }
      }
    }
  }
}
.HotVideoMain{
    width: 1200px;
    margin: 0 auto;
    margin-bottom: 50px;
    padding: 0 60px;
    box-sizing: border-box;
}
.HotVideoMain .leftVideo{
    width: 530px;
    float: left;
    box-sizing: border-box;
    position: relative;
    border-left: 1px solid #cec0a6;
    border-right: 1px solid #cec0a6;
    display: block;
    margin: 0 auto;
    box-sizing: border-box;
    &::after{
      content: '';
      width: 530px;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      position: absolute;
      bottom: -1px;
      left: 50%;
      transform: translateX(-50%);
    }
    &::before{
      content: '';
      width: 530px;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      position: absolute;
      top: -1px;
      left: 50%;
      transform: translateX(-50%) rotate(180deg);
    }
    > p{
      text-align: center;
      margin: 14px;
    }
}
.HotVideoMain .leftVideo img{
    width: 100%;
    margin: 0 auto;
}
.HotVideoMain .rightVideo{
    width: 530px;
    float: right;
    box-sizing: border-box;
    position: relative;
    border-left: 1px solid #cec0a6;
    border-right: 1px solid #cec0a6;
    display: block;
    margin: 0 auto;
    box-sizing: border-box;
    &::after{
      content: '';
      width: 530px;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      position: absolute;
      bottom: -1px;
      left: 50%;
      transform: translateX(-50%);
    }
    &::before{
      content: '';
      width: 530px;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      position: absolute;
      top: -1px;
      left: 50%;
      transform: translateX(-50%) rotate(180deg);
    }
    > p{
      text-align: center;
      margin: 14px;
    }
}
.HotVideoMain .rightVideo img{
    width: 100%;
}
.ENG{
  .top_ContactUs{
      /deep/ .ContactUs_table {
        margin-bottom: 20px;
        tr {
          td {
            width: 100%;
            h2{
              font-size: 30px;
            }
            > p:nth-child(2n){
              font-size: 18px;
              font-family: 'Domine-Bold' !important;
              color: #b59e72;
              margin-bottom: 5px;
            }
            > p:nth-child(2n+1){
              font-size: 18px;
              font-family: 'Domine-Regular' !important;
              color: #333333;
              line-height: 30px;
              margin-bottom: 15px;
            }
          }
          .ContactUs_table_td {
            height: 280px;
            margin-top: 0;
            .ContactUs_table_back{
              height: 250px;
              border-radius: 3px;
              p{
                font-size: 20px;
                font-family: 'Domine-Bold' !important;
                line-height: 36px;
              }
            }
          }
        }

      }

  }
}
</style>
