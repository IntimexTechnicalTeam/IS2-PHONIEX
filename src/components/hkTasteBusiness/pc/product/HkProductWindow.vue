<template>
  <div class="PcVersion" :class="{'ENG' : $Storage.get('locale') === 'E'}">
  <div class="productMain" v-if="item">
    <div class="in_pdWindow_page_item" :style="styla" @mouseenter="Enter=true" @mouseleave="Enter=false" @click="click">
      <div class="topWindowsImg imgbox">
        <img :src="(item.Image?item.Image:item.Img_L?item.Img_L:item.Img)"  :class="{'height_line':Enter}" :style="imgStyla" :data-key="item.Sku" @error="loadError" />
        <!-- <div class="shopMark">
            <div class="innerBox">
                <a  href="javascript:;"><i class="indexfav" v-bind:class="{'indexfav_hover':item.IsFavorite}"  v-on:click="addToFavorite(item)"></i><span v-on:click="addToFavorite(item)">{{$t('MyFavorite.MyFavorite')}}</span></a>
                <a  href="javascript:;" ><i class="showDetail" v-on:click="addCart(item)"></i><span v-on:click="addCart(item)">{{$t('home.ViewDetail')}}</span></a>
            </div>
        </div> -->
      </div>
        <div class="in_pdWindow_item_description">
          <div class="in_pdWindow_item_description_w">
            <a  href="javascript:;" class="in_pdWindow_item_title" v-on:click="addCart(item)">{{item.Name}}</a>
            <a  href="javascript:;"><i class="indexfav" v-bind:class="{'indexfav_hover':item.IsFavorite}"  v-on:click="addToFavorite(item)"></i><span v-on:click="addToFavorite(item)"></span></a>
          </div>

            <div class="in_pdWindow_item_price fix">
              <div class="pricebox">
                <inPrices :primePrices="item.ListPrice" :currentPrices="item.SalePrice" :currency="item.Currency" :DefaultListPrice="item.DefaultListPrice" :DefaultSalePrice="item.DefaultSalePrice" :DefaultCurrency="item.DefaultCurrency" size="small"></inPrices>
              </div>
              <a class="in_pdWindow_item_price_btn" href="javascript:;" ><span v-on:click="addCart(item)">{{$t('product.buy')}}</span></a>
            </div>
        </div>
    </div>
  </div>
  </div>
</template>
<script lang="ts">
import YouWouldLike from '@/model/youWouldLike';
import { Vue, Prop, Component } from 'vue-property-decorator';
@Component({ components: { inButton: () => import(/* webpackChunkName: "product" */ '@/components/base/pc/InsButton.vue'),
  inPrices: () => import(/* webpackChunkName: "product" */ '@/components/base/pc/InsPrices.vue') } })
export default class InsProductWindow extends Vue {
    private Enter:boolean = false;
    private newArray= [];
    @Prop() private item!:YouWouldLike;
    @Prop() private imgStyla!:string;
    @Prop() private styla!:string;
    buttonSubmit (item) {
      this.$router.push({
        path: '/product/detail',
        name: 'ProductsDetail',
        params: {
          id: item.Sku
        }
      });
    }
    addToFavorite (p) {
      if (p.IsFavorite) {
        this.$Api.member.removeFavorite(p.Sku).then((result) => {
          if (result.Succeeded) {
            p.IsFavorite = false;
            this.$message({
                message: this.$t('MyFavorite.RemoveSuccess') as string,
                type: 'error',
                customClass: 'messageboxNoraml'
              });
          }
        });
      } else {
        this.$Api.member.addFavorite(p.Sku).then((result) => {
          if (result.Succeeded) {
            p.IsFavorite = true;
            this.$message({
              message: this.$t('MyFavorite.AddSuccess') as string,
              type: 'success',
              customClass: 'messageboxNoraml'
            });
          } else {
            this.$router.push('/Account/login');
          }
        });
      }
    }
    addCart (val) {
      this.$router.push('/product/detail/' + val.Sku);
    }
    click (e) {
      let target = e.target as HTMLElement;
      if (target.nodeName === 'IMG') { this.$router.push('/product/detail/' + target.dataset.key); };
    }
    loadError (e) {
      e.target.src = '/static/Image/proddef.jpg';
    }
}
</script>
<style lang="less">
.messageboxNoraml{
  z-index: 1000000000!important;
}
</style>
<style lang="less">
.PcVersion .in_pdWindow_item_price .currentPricesMain ,.in_pdWindow_item_price .primePricesMain{
  width: 100%;
  display: flex;
  text-align: left;
  align-items: center;
  // height: 35px;
  padding-top: 4px;
}
.PcVersion .in_pdWindow_item_price .currentPricesMain  .small {
  font-size: 18px;
  word-break: break-all;
  text-align: center;
  color: #b59e72;
  display: inline-block;
  font-family: 'SourceHanSerifCN-Bold';
}
// .PcVersion .in_pdWindow_item_price .currentPricesMain .small:nth-child(2) {
//     font-size: 1.4rem;
//     color: #cd0909;
//     display: inline-block;
// }
.PcVersion .in_pdWindow_item_price .primePricesMain  .small {
  font-size: 14px;
  word-break: break-all;
  text-align: center;
  color: #999;
  display: inline-block;
  text-decoration:line-through;
  font-family: 'SourceHanSerifCN-Bold';
}
// .PcVersion .in_pdWindow_item_price .primePricesMain .small:nth-child(2) {
//   font-size: 1rem;
//   color: #999;
//   display: inline-block;
// }
.productMain:hover .in_pdWindow_page_item img {
    // border: 1px solid #cd0909;
    // border-radius: 5px;
    // display: block;
}
.productMain:hover .in_pdWindow_item_title {
    // transform: translateY(0);
    // color: #cd0909!important;
}
</style>
<style lang="less" scoped>
.imgbox{
    position: relative;
    display: block;
    width: 100%;
    overflow: hidden;
    border: 1px solid #dfcfb0;
    border-radius:5px;
    margin-bottom: 20px;
}

.imgbox:hover .shopMark{
    bottom: 0px;
}
.imgbox .shopMark{
    position: absolute;
    left: 0px;
    bottom: -100%;
    right: 0px;
    height:100%;
    background:rgba(0,0,0,.5);
    transition: all .3s;
    display: flex;
    align-items: center;
    justify-content: center;
    .innerBox{
      width: 100%;
      display: inline-block;
    }
}
.imgbox .shopMark a{
    text-align: center;
    display: block;
}
.imgbox .shopMark a:hover{
  // transform: translateY(-3px);
}
.imgbox .shopMark a:hover span{
    // text-decoration: underline;
}
.imgbox .shopMark a:nth-child(1){
    // padding-top: 60px;
    // padding-bottom: 20px;
}
.imgbox .shopMark a span{
    color: #FFF;
    font-size: 16px;
    margin-top: 5px;
    display: block;
}
.in_pdWindow_item_description_w i{
    width: 20px;
    height: 17px;
    display: block;
    margin: 0 auto;
}
.in_pdWindow_item_description_w .indexfav{
    background: url(/images/pc/index_42.png) no-repeat center center;
    background-size: 100%;
}
.in_pdWindow_item_description_w .indexfav_hover{
    background: url(/images/pc/index_42hover.png) no-repeat center center!important;
    background-size: 100%;
}
.imgbox .shopMark .showDetail{
    background: url(/images/pc/viewDetail.png) no-repeat center center;
    background-size: 100%;
}

.imgbox img{
    width: 100%;
    transition: border all 1s;
    box-sizing: border-box;
    display: block;
}
.imgbox img:hover{
    // border:1px solid #e02533;
}
.in_pdWindow_page_item img {
  box-sizing: border-box;
  cursor: pointer;

}
.height_line {
  // border: 1px solid black !important;
}
.in_pdWindow_item_title {
    font-size: 20px;
    width: 100%;
    margin: 0 auto;
    word-break: break-all;
    text-align: left;
    color: #4d4d4d  ;
    display: block;
    // line-height: 25px;
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    word-break: break-word;
    // margin-top: 10px;
    // margin-bottom: 10px;
    font-family: 'SourceHanSerifCN-Bold';
}
.in_pdWindow_item_code {
  margin-top: 1rem;
  color: #999999;
  text-align: center;
}
.in_pdWindow_item_description{
  margin-bottom: 30px;
  .in_pdWindow_item_description_w{
    display: flex;
    align-items: center;

  }
}
.in_pdWindow_item_price{
  margin-top: 8px;
  .pricebox{
    float: left;
  }
  .in_pdWindow_item_price_btn{
    float: right;
    width: 100px;
    height: 35px;
    background-color: #fff;
    border: 1px solid #b59e72;
    border-radius: 3px;
    text-align: center;
    transition: all 0.3s;
    span{
      font-size: 18px;
      font-family: 'SourceHanSerifCN-Bold';
      color: #b59e72;
      line-height: 35px;
    }
    &:hover{
      background-color: #b59e72;
      border: 1px solid #b59e72;
      span{
        color: #fff;
      }
    }
  }
}
.ENG{
  .in_pdWindow_item_title{
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    word-break: break-word;
  }
  .in_pdWindow_item_description .in_pdWindow_item_description_w{
    align-items: initial;
    .indexfav{
      margin-top: 3px;
    }
  }
}
</style>
