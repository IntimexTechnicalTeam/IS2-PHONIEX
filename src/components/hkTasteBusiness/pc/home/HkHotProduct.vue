<template>
    <div class="indexSale">
        <div class="TitleBg">
          <div class="line"></div>
          <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
            <!-- <h2>{{$t('Cms.commodity')}}</h2> -->
            <img src="/images/pc/M-indexC_07.png" alt="">
          </div>
            <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
              <img src="/images/pc/Mindex_06.png" alt="">
            </div>
          </div>
        <div class="indexHotSale">
          <div class="perSale" v-for="(val,index) in HotSales" :key="index">
                   <inProductWindow :item="val"  style="width:100%;"></inProductWindow>
            </div>
        </div>
        <div class="more">
          <router-link to="/product/search/-">{{$t('product.Viewmore')}}</router-link>
        </div>
    </div>
</template>
<script lang="ts">
import { Vue, Prop, Component } from 'vue-property-decorator';
import inProductWindow from '@/components/hkTasteBusiness/pc/product/HkProductWindow.vue';
@Component({
  components: {
    inProductWindow
  }
})
export default class PkHotProduct extends Vue {
    HotSales:any[]=[];
    loadHotProducts () {
      var page = 'Home';
      this.$Api.promotion.getPromotion('Home', 4).then((result) => {
        if (result.Promotion.PrmtProductList.length > 0) {
          this.HotSales = result.Promotion.PrmtProductList.slice(0, 8);
        }
      });
    }
    get lang () {
      return this.$Storage.get('locale');
    }
    mounted () {
      this.loadHotProducts();
    }
}
</script>
<style lang="less" scoped>
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
    //   background: url(/images/pc/index_40.png) no-repeat center center;
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
.indexSale{
    width: 100%;
    margin: 0 auto;
    // padding-top: 110px;
    .more{
      width: 254px;
      height: 50px;
      background: url('/images/pc/index_66.png') no-repeat center center;
      margin: 0 auto;
      text-align: center;
      margin-top: 14px;
      >a{
        font-size: 20px;
        font-family: 'SourceHanSerifCN-Medium';
        color: #b59e72;
        line-height: 50px;
      }
    }
}
.indexSaleTitle{
    background: url('/images/pc/index_27.png') no-repeat center center;
    width: 544px;
    height: 114px;
    background-size: 100%;
    margin: 0 auto;
    margin-bottom: 30px;
}
.indexSaleTitleE{
    background: url('/images/pc/bigsales.png') no-repeat center center!important;
    width: 544px;
    height: 114px;
    background-size: 100%;
    margin: 0 auto;
    margin-bottom: 30px;
}
.indexHotSale{
    width: 1200px;
    margin: 0 auto;
    display: flex;
    flex-wrap: wrap;
    margin-top: 51px;
}
.perSale{
    box-sizing: border-box;
    width: 270px;
    float: left;
    margin: 0 15px;
    // padding: 1.5%;
}
// .perSale:nth-child(4n){
//      margin-right: 0%!important;
// }
</style>
