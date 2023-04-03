<template>
  <div class="productBox" :class="{'ENG' : $Storage.get('locale') === 'E'}">
     <div class="TitleBg">
          <div class="line"></div>
          <div class="innerBox" v-if="$Storage.get('locale') === 'C'">
            <!-- <h2>{{$t('Cms.commodity')}}</h2> -->
            <img src="/images/mobile/M-indexC_07.png" alt="">
          </div>
            <div class="innerBox" v-if="$Storage.get('locale') === 'E'">
              <img src="/images/mobile/Mindex_06.png" alt="">
            </div>
      </div>
    <div class="swiper-container swiper-container-hot fix">
      <div class="perSale" v-for="(val,index) in hotProducts" :key="index">
          <inProductWindow :item="val" ></inProductWindow>
      </div>
    </div>
    <div class="more">
      <router-link to="/product/search/-">{{$t('product.Viewmore')}}</router-link>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue, Prop, Component } from 'vue-property-decorator';
import inProductWindow from '@/components/hkTasteBusiness/mobile/product/HkProductWindow.vue';
import { swiper, swiperSlide } from 'vue-awesome-swiper/src';
@Component({
  components: {
    inProductWindow,
    swiper,
    swiperSlide
  }
})
export default class PkHotProduct extends Vue {
    hotProducts:any[]=[];
    bannerImg: string = '';
    // swiperOption: object = {
    //   pagination: {
    //     el: '.swiper-pagination',
    //     clickable: true
    //   },
    //   scrollbar: {
    //     el: '.swiper-scrollbar'
    //   },
    //   navigation: {
    //     nextEl: '.swiper-button-next',
    //     prevEl: '.swiper-button-prev'
    //   },
    //   slidesPerView: 2
    // };
    loadHotProducts () {
      var page = 'Home';
      this.$Api.promotion.getPromotion('Home', 4).then((result) => {
        if (result.Promotion.PrmtProductList.length > 0) {
          this.hotProducts = result.Promotion.PrmtProductList.slice(0, 8);
        }
      });
    }
    mounted () {
      this.loadHotProducts();
    }
}
</script>
<style>
.swiper-scrollbar {
    border-radius:0px!important;
    position: relative;
    background: #fff!important;
}
.swiper-scrollbar-drag{
      background: #666666;
      border-radius: 0px;
}
.swiper-container-horizontal > .swiper-scrollbar{
      height: 8px!important;
}
.productBox  .insProductHot img{
    width: 85%!important;
    margin: 0 auto;
    display: block;
}
</style>
<style lang="less" scoped>
.TitleBg{
  position: relative;
  .innerBox{
    position: absolute;
    left: 50%;
    transform: translate(-50%, -50%);
    top: 50%;
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
    img{
        display: block;
        height: 5rem;
      }
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
.productBox {
  margin-top: 3rem;
  .more{
      width: 254px;
      height: 50px;
      background: url('/images/pc/index_66.png') no-repeat center center;
      margin: 0 auto;
      text-align: center;
      margin-top: 14px;
      >a{
        font-size: 1.4rem;
        font-family: 'SourceHanSerifCN-Medium';
        color: #b59e72;
        line-height: 50px;
      }
    }
}
.productBox a{
    text-decoration: none;
    color: #383838;
    display: block;
}
.productBox .ProductName{
    font-size: 1.8rem;
    width: 90%;
    margin: 0 auto;
    word-break: break-all;
    text-align: center;
    padding-bottom: 1rem;
    padding-top: 1rem;
    color:#0b0b0b;
}
.productBox .ProductPrice{
    font-size: 1.5rem;
    width: 90%;
    margin:0 auto;
    word-break: break-all;
    text-align: center;
    color:#0b0b0b;
    span{
      font-size: 1.8rem;
      color:#cd0909;
    }
}
.productBox .swiper-container {
  width: 94%;
  margin: 0 auto;
  margin-top: 4rem;
  .perSale{
    width: 48%;
    float: left;
    margin-right: 4%;
    margin-bottom: 1.5em;
    &:nth-child(2n){
      margin-right: 0;
    }
  }
}
.productBox .swiper-wrapper {
  // height: 22rem;
}
.productBox_title {
    font-size: 2.4rem;
    text-align: center;
    text-transform: uppercase;
    color: #0b0b0b;
    margin-bottom: 5rem;
    margin-top: 5rem;
}
.gradient {
    position: relative;
    border-bottom: 1px transparent solid;
    -o-border-image: linear-gradient(to right, #fff, #3d3d3d, #fff) 10;
    border-image: -webkit-gradient(linear, left top, right top, from(#fff), color-stop(#3d3d3d), to(#fff)) 10;
    border-image: linear-gradient(to right, #fff, #3d3d3d, #fff) 10;
}
.BannerImg{
    width: 85%;
    margin: 0 auto;
    display: block;
    border: 1px solid #000;
    border-radius: 10px;
}

</style>
