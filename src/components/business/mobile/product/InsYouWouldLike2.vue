<template>
  <div :style="styla" v-if="ShowItemsLength>0" class="MobileVersionYouLike">
    <div class="in_slider_title">
      <div class="line"></div>
      <div class="back" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{title}}</h2> -->
        <img src="/images/mobile/You Might LikeC.png" alt="">
      </div>
      <div class="back" v-if="$Storage.get('locale') === 'E'">
        <img src="/images/mobile/You Might Like.png" alt="">
      </div>
    </div>
    <swiper :options="SwiperOptionYouLike" ref="mySwiper">
      <!-- slides -->
      <swiperSlide v-for="(item,idx) in InnerItems" :key="idx">
        <inProductWindow :item="item" :imgStyla="imgStyla" styla="width:100%;margin:0 auto;padding-bottom:2rem;margin-bottom: 3rem;"></inProductWindow>
      </swiperSlide>
      <!-- <div class="swiper-scrollbar"   slot="scrollbar"></div> -->
      <div class="swiper-button-prev swiper-button-prevyou" slot="button-prev"></div>
      <div class="swiper-button-next swiper-button-nextyou" slot="button-next"></div>
    </swiper>
  </div>
</template>
<script lang="ts">
import YouWouldLike from '@/model/youWouldLike';
import inButton from '@/components/base/mobile/InsButton.vue';
import inProductWindow from '@/components/hkTasteBusiness/mobile/product/HkProductWindow.vue';
import Currency from '@/model/currency';
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import { swiper, swiperSlide } from 'vue-awesome-swiper/src';
@Component({
  components: {
    inButton, inProductWindow, swiper, swiperSlide
  }
})
export default class InsYouWouldLike2 extends Vue {
  // data
  private SwiperOptionYouLike = {
    navigation: {
      nextEl: '.swiper-button-next.swiper-button-nextyou',
      prevEl: '.swiper-button-prev.swiper-button-prevyou'
    },
    slidesPerView: 2,
    spaceBetween: 20
  };
  private InnerItems: YouWouldLike[] = [];
  private InnerItemsCopy: YouWouldLike[] = [];
  private ShowItems: YouWouldLike[][] = [];
  private Layer: boolean = false;
  private ShowItemsLength: number = 0;
  //   props
  @Prop() private readonly styla!: string;
  @Prop() private readonly imgStyla!: string;
  @Prop() private readonly title!: string;
  // @Prop() private readonly pageNum!: number;
  // @Prop() private readonly items!: YouWouldLike[];
  @Prop() private readonly ProductSku!: string;
  //   method
  click (e: MouseEvent) {
    let target = e.target as HTMLElement;
    if (target.nodeName === 'IMG') {
    }
  }
  buttonClick (item: YouWouldLike) {
    console.log(item);
  }
  created () {
    this.$Api.product.getRltProduct(this.ProductSku).then((result) => { this.InnerItems = result.YouWouldLike; this.ShowItemsLength = result.YouWouldLike.length; });
  }
  @Watch('ProductSku')
  onProductSkuChange (o, n) {
    this.$Api.product.getRltProduct(this.ProductSku).then(result => {
      this.InnerItems = result.YouWouldLike;
      this.ShowItemsLength = result.YouWouldLike.length;
    });
  }
}
</script>
<style>
.MobileVersionYouLike .swiper-container{
  width: 94%;
  margin: 0 auto;
}
.MobileVersionYouLike .swiper-button-prev{
  background-image:url('/images/pc/cleft.png')!important;
  left:-1px!important;
}
.MobileVersionYouLike .swiper-button-next{
    background-image:url('/images/pc/cright.png')!important;
    right:1px!important;
}
.MobileVersionYouLike  .swiper-button-prev,.MobileVersionYouLike  .swiper-button-next{
    width: 3rem!important;
    height: 3rem!important;
    background-size:100%!important;
    margin-top: -8rem;
    opacity: 1;
}
.swiper-scrollbar {
   border-radius:0px!important;
    position: relative;
    background: #f2f2f2!important;
}
.swiper-scrollbar-drag{
      background: #d9d9d9;
}
.swiper-container-horizontal > .swiper-scrollbar{
      height: 10px!important;
}
</style>
<style scoped lang="less">
.in_slider_title {
  text-align: center;
  margin: 4rem 0;
  // font-size: 2rem;
  position: relative;
  .line{
      width: 100%;
      height: 19px;
      background: url(/images/pc/index_27.png) center center;

    }
  .back{
      position: absolute;
      left: 50%;
      transform: translate(-50%,-50%);
      top: 50%;
      width: 100%;
      img{
        // display: block;
        height: 5rem;
      }
      // &::after{
      //   content: '';
      //   width: 24rem;
      //   height: 80px;
      //   background: url(/images/pc/Product-Detail_title.png) no-repeat center center;
      //   background-size: contain;
      //   position: absolute;
      //   left: 50%;
      //   transform: translateX(-50%);
      //   top: -16px;
      // }
      h2{
        font-size: 26px;
        color: #fff;
        text-align: center;
        font-family: 'SourceHanSerifCN-Bold';
        position: relative;
        z-index: 10;
        letter-spacing: 20px;
        padding-left: 20px;
        padding-top: 2px;
      }
    }
}
.in_slider_page_container {
  box-sizing: border-box;
  display: flex;
  flex-wrap: nowrap;
  width: 100%;
  padding: 0 4rem;
  user-select: none;
}
.in_slider_page_item {
  width: 100%;
}
</style>
