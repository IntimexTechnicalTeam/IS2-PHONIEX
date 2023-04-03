<template>
  <div class="indexHotVideo" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="TitleBg">
      <div class="line"></div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
          <!-- <h2>{{paymentTitle}}</h2> -->
          <img src="/images/pc/M-indexC_12.png" alt="">
        </div>
        <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
          <img src="/images/pc/Mindex_13.png" alt="">
        </div>
    </div>
    <div class="HotVideoMain">
      <div class="leftVideo">
        <p v-html="paymentContent.Body"></p>
      </div>
    </div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class PkLiveBox extends Vue {
  paymentContent:string='';
  paymentTitle: string='';
  getVideoContent () {
    this.$Api.cms.getContentByDevice({ Key: 'About', IsMobile: false }).then(result => {
      this.paymentContent = result.CMS;
      this.paymentTitle = result.CMS.Title;
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  created () {
    this.getVideoContent();
  }
}
</script>

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
    display: block;
    // padding-top: 80px;
    // padding-bottom: 80px;
}
.HotVideoMain{
    width: 1200px;
    margin: 0 auto;
}
.HotVideoMain .leftVideo{
    width: 1200px;
    margin: 0 auto;
    margin-top: 70px;
    /deep/ p{
      table.about_table{
        width: 980px;
        margin: 0 auto;
        tr{
          display: block;
          margin-bottom: 40px;
          td{
            padding: 20px 40px;
            display: inline-block;
            width: 50%;
            font-size: 20px;
            line-height: 48px;
            box-sizing: border-box;
            text-align: justify;
            img{
              border-radius: 5px;
              display: block;
              box-sizing: border-box;
              width: auto;
              margin: 0 auto;
            }
          }
          td.about_img{
            position: relative;
            border-left: 1px solid #cec0a6;
            border-right: 1px solid #cec0a6;
            display: inline-table;
            padding: 20px 13px;
            width: 450px;
            box-sizing: border-box;
            img{
              width: 420px;
              height: 312px;
              display: block;
              margin: 0 auto;
            }
            &::after{
              content: '';
              width: 450px;
              height: 25px;
              background: url('/images/pc/backline_09.png') no-repeat bottom;
              position: absolute;
              bottom: -1px;
              left: 50%;
              transform: translateX(-50%);
            }
            &::before{
              content: '';
              width: 450px;
              height: 25px;
              background: url('/images/pc/backline_09.png') no-repeat bottom;
              position: absolute;
              top: -1px;
              left: 50%;
              transform: translateX(-50%) rotate(180deg);
            }
          }
          &:first-child{
            td:last-child{
              padding-right: 0;
            }
          }
        }
      }
    }
}
.HotVideoMain .leftVideo img{
    width: 100%;
    margin: 0 auto;
}
.HotVideoMain .rightVideo{
    width: 40%;
    float: left;
    background-size: 100% 100%;
    box-sizing: border-box;
    padding: 30px;
}
.HotVideoMain .rightVideo img{
    width: 100%;
}
.ENG{
  .HotVideoMain .leftVideo /deep/ p table.about_table tr td{
    font-size: 18px;
    line-height: 36px;
  }

}
</style>
