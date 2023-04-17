<template>
 <div class="BranchMain" :class="{'ENG' : $Storage.get('locale') === 'E'}">
   <div class="InnerSide">
      <div class="TitleBg">
        <div class="line"></div>
        <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
          <!-- <h2>{{paymentTitle}}</h2> -->
          <img src="/images/mobile/M-indexC_12.png" alt="">
        </div>
        <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
          <img src="/images/mobile/Mindex_13.png" alt="">
        </div>
      </div>
      <div class="IndexShopMain">
          <p v-html="paymentContent.Body"></p>
      </div>
   </div>
 </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component
export default class PkNews extends Vue {
  paymentContent:string='';
  paymentTitle: string='';
  getVideoContent () {
    this.$Api.cms.getContentByDevice({ Key: 'About', IsMobile: true }).then(result => {
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
.clear{
  clear: both;
}
.TitleBg{
  position: relative;
  .innerBox{
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
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
    height: 16px;
    background: url(/images/pc/index_27.png) center center;
    background-size: contain;
  }
}
.BranchMain{
  .IndexShopMain{
    margin-top: 4rem;
    /deep/ p{
      table.about_table{
        width: 94%;
        margin: 0 auto;
        tr{
          display: block;
          margin-bottom: 1rem;
          td{
            padding: 1rem;
            display: inline-block;
            width: 100%;
            font-size: 1.4rem;
            line-height: 2.2rem;
            box-sizing: border-box;
            text-align: justify;
            color: #333333;
            word-break: break-word;
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
            padding: 1rem;
            width: 100%;
            box-sizing: border-box;
            img{
              width: 100%;
              // height: 312px;
              display: block;
              margin: 0 auto;
            }
            &::after{
              content: '';
              width: 100%;
              height: 25px;
              background: url('/images/pc/backline_09.png') no-repeat bottom center;
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
              background: url('/images/pc/backline_09.png') no-repeat bottom center;
              background-size: contain;
              position: absolute;
              top: -1px;
              left: 50%;
              transform: translateX(-50%) rotate(180deg);
            }
          }
        }
      }
    }
  }
}
.ENG.BranchMain {
   .IndexShopMain /deep/ p table.about_table tr td{
    font-size: 1.2rem;
    line-height: 1.8rem;
  }
}
</style>
