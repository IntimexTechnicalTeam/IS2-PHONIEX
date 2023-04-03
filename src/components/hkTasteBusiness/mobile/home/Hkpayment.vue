<template>
  <div class="indexHotVideo">
    <div class="TitleBg">
      <div class="line"></div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{paymentTitle}}</h2> -->
        <img src="/images/mobile/M-indexC_10.png" alt="">
      </div>
      <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
        <img src="/images/mobile/Mindex_09.png" alt="">
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
    this.$Api.cms.getContentByDevice({ Key: 'payment', IsMobile: true }).then(result => {
      this.paymentContent = result.CMS;
      this.paymentTitle = result.CMS.Title;
      console.log(result, '付款');
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
      height: 5rem;
      display: block;
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
.indexHotVideo{
    width: 100%;
    display: block;
    padding-top: 3rem;
    padding-bottom: 3rem;
}
.HotVideoMain{
    width: 94%;
    margin: 0 auto;
}
.HotVideoMain .leftVideo{
    width: 100%;
    margin: 0 auto;
    margin-top: 4rem;
    /deep/ p{
      table{
        width: 100%;
        tr{
          td{
            padding: 3px;
            display: table-cell;
            width: 25%;
            img{
              border: 1px solid #e6e6e6;
              border-radius: 3px;
              display: block;
              box-sizing: border-box;
              width: 100%;
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
.indexVideoTitle{
    width: 544px;
    height: 114px;
    background-size: 100%;
    margin: 0 auto;
    margin-bottom: 70px;
}
.indexVideoTitleE{
    width: 544px;
    height: 114px;
    background-size: 100%;
    margin: 0 auto;
    margin-bottom: 70px;
}
</style>
