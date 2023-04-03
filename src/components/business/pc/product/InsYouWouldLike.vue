<template>
  <div :style="styla" v-if="ShowItemsLength>0" class="PcVersionYouLike">
    <div class="in_slider_title">
      <div class="line"></div>
      <div class="back" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{title}}</h2> -->
        <img src="/images/pc/You Might LikeC.png" alt="">
      </div>
      <div class="back" v-if="$Storage.get('locale') === 'E'">
        <img src="/images/pc/You Might Like.png" alt="">
      </div>
    </div>
    <swiper :options="SwiperOptionYouLike" ref="mySwiper" class="SwiperOptionYouLike">
      <!-- slides -->
      <swiperSlide v-for="(page,idx) in ShowItems" :key="idx">
        <div class="in_slider_page_container" @click="click">
          <div class="in_slider_page_item" v-for="(item,index) in page" :key="index">
            <div class="in_slider_page_item" v-if="!item.virtual">
              <inProductWindow :item="item" :imgStyla="imgStyla" styla="width:270px;margin:0 auto;"></inProductWindow>
            </div>
          </div>
        </div>
      </swiperSlide>
      <div class="swiper-button-prev" slot="button-prev"></div>
      <div class="swiper-button-next" slot="button-next"></div>
    </swiper>
  </div>
</template>
<script lang="ts">
import YouWouldLike from '@/model/youWouldLike';
import inButton from '@/components/base/pc/InsButton.vue';
import inProductWindow from '@/components/hkTasteBusiness/pc/product/HkProductWindow.vue';
import Currency from '@/model/currency';
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import { swiper, swiperSlide } from 'vue-awesome-swiper/src';
@Component({
  components: {
    inButton, inProductWindow, swiper, swiperSlide
  }
})
export default class InsYouWouldLike extends Vue {
  // data
  private SwiperOptionYouLike = {
    navigation: {
      nextEl: '.swiper-button-next',
      prevEl: '.swiper-button-prev'
    }
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
  @Prop() private readonly pageNum!: number;
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
  @Watch('InnerItems')
  onInnerItemsChange (o, n) {
    this.InnerItemsCopy = this.InnerItems.slice();
    this.ShowItems.splice(0, this.ShowItems.length);
    while (this.InnerItemsCopy.length > 0) {
      this.ShowItems.push(this.InnerItemsCopy.splice(0, this.pageNum));
    }
    while (
      this.ShowItems.length > 0 &&
      this.ShowItems[this.ShowItems.length - 1].length < this.pageNum
    ) {
      this.ShowItems[this.ShowItems.length - 1].push(
        new YouWouldLike('-1', '', '', '', '', '', new Currency(), '', '', new Currency(), true)
      );
    }
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
.PcVersionYouLike .swiper-button-prev{
  background-image:url('/images/pc/cleft.png')!important;
  left:15px!important;
}
.PcVersionYouLike .swiper-button-next{
    background-image:url('/images/pc/cright.png')!important;
    right:13px!important;
}
.PcVersionYouLike  .swiper-button-prev,.PcVersionYouLike  .swiper-button-next{
    width: 50px!important;
    height: 50px!important;
    background-size:100%!important;
    margin-top: -80px;
    opacity: 1;
}
</style>
<style scoped lang="less">

.in_slider_title {
  text-align: center;
  margin: 50px 0;
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
      transform: translate(-50%, -50%);
      top: 50%;
      img{
        height: 80px;
        display: block;
      }
      // &::after{
      //   content: '';
      //   width: 332px;
      //   height: 80px;
      //   background: url(/images/pc/Product-Detail_title.png) no-repeat center center;
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
}
.in_slider_page_container {
  box-sizing: border-box;
  display: flex;
  flex-wrap: nowrap;
  width: 100%;
  margin: 0 auto;
  user-select: none;
}
.in_slider_page_item {
   width: 100%;
}
.SwiperOptionYouLike{
  width: 1200px;
  margin: 0 auto;
}
</style>
