<template>
  <div class="indexHotVideo" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="TitleBg">
      <div class="line"></div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{$t('regnpay.ContactUs')}}</h2> -->
        <img src="/images/mobile/M-indexC_14.png" alt="">
      </div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
          <img src="/images/mobile/Mindex_16.png" alt="">
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
    this.$Api.cms.getContentByDevice({ Key: 'fb', IsMobile: true }).then(result => {
      this.videoContent = result.CMS;
    });
  }
  getFbContent () {
    this.$Api.cms.getContentByDevice({ Key: 'Instagram', IsMobile: true }).then(result => {
      this.fbContent = result.CMS;
    });
  }
  gettopContactUs () {
    this.$Api.cms.getContentByDevice({ Key: 'ContactUs', IsMobile: true }).then(result => {
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
  width: 320px;
  margin: 0 auto;
  height: 600px;
  border-radius: 5px;
  border: 1px solid #edecf1;
  box-sizing: border-box;
}

.HotVideoMain .rightVideo iframe{
  width: 100%;
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
      display: block;
      height: 5rem;
    }
    // &::after{
    //   content: '';
    //   width: 220px;
    //   height: 70px;
    //   background: url(/images/pc/index_24.png) no-repeat center center;
    //   background-size: contain;
    //   position: absolute;
    //   left: 50%;
    //   transform: translateX(-50%);
    //   top: -15px;
    // }
    h2{
      font-size: 26px;
      color: #fff;
      text-align: center;
      font-family: 'SourceHanSerifCN-Bold';
      position: relative;
      z-index: 10;
      letter-spacing: 18px;
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
    padding-top: 3rem;
    // padding-bottom: 80px;
}
.top_ContactUs{
  width: 100%;
  margin: 0 auto;
  padding: 1.5rem;
  padding-bottom: 0;
  box-sizing: border-box;
  /deep/ .ContactUs_table{
    width: 100%;
    margin-top: 2rem;
    tr{
      td{
        width: 100%;
        h2{
          font-family: 'SourceHanSerifCN-Bold';
          font-size: 2.4rem;
          margin-bottom: 2rem;
          color: #b59e72;
          text-align: center;
        }
        >p{
          font-size: 1.4rem;
          color: #4e4e4e;
          margin-bottom: 2rem;
          // display: flex;
          // align-items: center;
          text-align: center;

          &:nth-child(2n){
            width: 100%;
            display: flex;
            color: #b59e72;
            font-size: 22px;
            font-family: 'SourceHanSerifCN-Bold';
            align-items: center;
            justify-content: center;
            margin-bottom: 0.5rem;
          }
          img{
            // margin-right: 10px;
          }
        }
      }
      .ContactUs_table_td{
        width: 100%;
        // height: 180px;
        position: relative;
        border-left: 1px solid #cec0a6;
        border-right: 1px solid #cec0a6;
        display: block;
        // margin-left: auto;
        box-sizing: border-box;
        padding: 1rem;
        &::after{
              content: '';
              width: 100%;
              height: 25px;
              background: url('/images/pc/backline_13.png') no-repeat bottom;
              background-size: contain;
              position: absolute;
              bottom: -1px;
              left: 50%;
              transform: translateX(-50%);
            }
            &::before{
              content: '';
              width: 100%;
              height: 25px;
              background: url('/images/pc/backline_13.png') no-repeat bottom;
              background-size: contain;
              position: absolute;
              top: -1px;
              left: 50%;
              transform: translateX(-50%) rotate(180deg);
            }
        p{
          color: #fff;
          font-size: 1.4rem;
          font-family: 'SourceHanSerifCN-Bold';
          line-height: 30px;
          text-align: center;
          justify-content: center;
          margin: 0;
        }
        .ContactUs_table_back{
          width: 100%;
          // height: 150px;
          text-align: center;
          background: url('/images/mobile/index_115.png') center center;
          display: block;
          // margin: 14px;
          padding: 1rem;
          box-sizing: border-box;
        }
      }
    }
  }
}
.HotVideoMain{
    width: 100%;
    margin: 0 auto;
    margin-top: 2rem;
    margin-bottom: 2rem;
    padding: 0 2rem;
    box-sizing: border-box;
}
.HotVideoMain .leftVideo{
    width: 100%;
    // float: left;
    box-sizing: border-box;
    position: relative;
    border-left: 1px solid #cec0a6;
    border-right: 1px solid #cec0a6;
    display: block;
    margin: 0 auto;
    box-sizing: border-box;
    margin-bottom: 2rem;
    &::after{
      content: '';
      width: 100%;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      background-size: contain;
      position: absolute;
      bottom: -1px;
      left: 50%;
      transform: translateX(-50%);
    }
    &::before{
      content: '';
      width: 100%;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      background-size: contain;
      position: absolute;
      top: -1px;
      left: 50%;
      transform: translateX(-50%) rotate(180deg);
    }
    > p{
      text-align: center;
      padding: 14px;
    }
}
.HotVideoMain .leftVideo img{
    width: 100%;
    margin: 0 auto;
}
.HotVideoMain .rightVideo{
    width: 100%;
    // float: right;
    box-sizing: border-box;
    position: relative;
    border-left: 1px solid #cec0a6;
    border-right: 1px solid #cec0a6;
    display: block;
    margin: 0 auto;
    box-sizing: border-box;
    padding: 1rem;
    &::after{
      content: '';
      width: 100%;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      background-size: contain;
      position: absolute;
      bottom: -1px;
      left: 50%;
      transform: translateX(-50%);
    }
    &::before{
      content: '';
      width: 100%;
      height: 25px;
      background: url('/images/pc/backline_13.png') no-repeat bottom;
      background-size: contain;
      position: absolute;
      top: -1px;
      left: 50%;
      transform: translateX(-50%) rotate(180deg);
    }
    > p{
      text-align: center;
      padding: 0.5rem;
    }
}
.HotVideoMain .rightVideo img{
    width: 100%;
}
.ENG {
  .top_ContactUs/deep/ .ContactUs_table tr td h2{
    font-family: 'Domine-Bold';
    font-size: 1.6rem;
  }
  .top_ContactUs/deep/ .ContactUs_table tr td > p:nth-child(2n){
    font-size: 1.3rem;
  }
  .top_ContactUs/deep/ .ContactUs_table tr td > p{
    font-size: 1.2rem;
  }
}
</style>
