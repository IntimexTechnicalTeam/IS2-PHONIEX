<template>
    <div class="in_pdWindow_page_item" :class="{'ENG' : $Storage.get('locale') === 'E'}" :style="styla" @mouseenter="Enter=true" @mouseleave="Enter=false" @click="click" v-if="item">
        <img :src="(item.Image?item.Image:item.Img_L?item.Img_L:item.Img)"  :class="{'height_line':Enter}" :style="imgStyla" :data-key="item.Sku" @error="loadError" />
        <div class="in_pdWindow_item_description">
          <div class="in_pdWindow_item_description_w">
            <router-link :to="'/product/detail/'+item.Sku" class="in_pdWindow_item_title" >{{item.Name}}</router-link >
            <a  href="javascript:;"><i class="indexfav" v-bind:class="{'indexfav_hover':item.IsFavorite}"  v-on:click="addToFavorite(item)"></i><span v-on:click="addToFavorite(item)"></span></a>
          </div>

            <!-- <div class="in_pdWindow_item_code">&nbsp;{{item.Code}}</div> -->
            <div class="in_pdWindow_item_price">
              <div class="pricebox">
                <inPrices :primePrices="item.ListPrice" :currentPrices="item.SalePrice" :currency="item.Currency" :DefaultListPrice="item.DefaultListPrice" :DefaultSalePrice="item.DefaultSalePrice" :DefaultCurrency="item.DefaultCurrency" size="small"></inPrices>
              </div>
               <a class="in_pdWindow_item_price_btn" href="javascript:;" ><span v-on:click="addCart(item)">{{$t('product.buy')}}</span></a>
            </div>
        </div>
    </div>
</template>
<script lang="ts">
import inButton from '@/components/base/mobile/InsButton.vue';
import inPrices from '@/components/base/mobile/InsPrices.vue';
import YouWouldLike from '@/model/youWouldLike';
import { Vue, Prop, Component } from 'vue-property-decorator';
@Component({ components: { inButton, inPrices } })
export default class InsProductWindow extends Vue {
    private Enter:boolean = false;
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
    click (e) {
      let target = e.target as HTMLElement;
      if (target.nodeName === 'IMG') { this.$router.push('/product/detail/' + target.dataset.key); };
    }
    loadError (e) {
      e.target.src = '/static/Image/proddef.jpg';
    }
    addCart (val) {
      this.$router.push('/product/detail/' + val.Sku);
    }
}
</script>
<style lang="less">
.in_pdWindow_item_price .currentPricesMain ,.in_pdWindow_item_price .primePricesMain{
  width: 100%;
  display: inline-block;
  text-align: left;
}
.in_pdWindow_item_price .currentPricesMain  .small {
  font-size: 1.4rem;
  word-break: break-all;
  text-align: left;
  color: #b59e72;
  display: inline-block;
}
// .in_pdWindow_item_price .currentPricesMain .small:nth-child(2) {
//     font-size: 1.4rem;
//     color: #cd0909;
//     display: inline-block;
// }
.in_pdWindow_item_price .primePricesMain  .small {
  font-size: 1.2rem;
  word-break: break-all;
  text-align: left;
  color: #999;
  display: inline-block;
  text-decoration: line-through;
}
// .in_pdWindow_item_price .primePricesMain .small:nth-child(2) {
//     font-size: 1.2rem;
//     color: #999;
//   display: inline-block;
// }
.ENG .in_pdWindow_item_price .currentPricesMain .small{
  font-size: 1.2rem;
}
</style>
<style lang="less" scoped>
.in_pdWindow_page_item img {
  box-sizing: border-box;
  cursor: pointer;
  width: 100%;
  border: 1px solid #dfcfb0;
  border-radius: 5px;
}
// .height_line {
//   border: 1px solid black !important;
// }
.in_pdWindow_item_title {
    font-size: 1.4rem;
    width: 100%;
    margin: 0 auto;
    text-align: left;
    color: #4d4d4d;
    // line-height: 25px;
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    word-break: break-word;
    // margin-top: .5rem;
    // margin-bottom: .5rem;
    font-family: 'SourceHanSerifCN-Bold';
}
.in_pdWindow_item_code {
  margin-top: 1rem;
  color: #999999;
  text-align: center;
}
.in_pdWindow_item_description_w{
    display: flex;
    align-items: center;
    margin-bottom: 0.5rem;
    margin-top: 0.5rem;
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
.in_pdWindow_item_price {
  .pricebox{
    margin-bottom: 1rem;
  }
  .in_pdWindow_item_price_btn{
    width: 100%;
    height: 3rem;
    background-color: #b59e72;
    text-align: center;

    border-radius: 3px;
    display: block;
    span{
      line-height: 3rem;
    color: #fff;
    font-size: 1.3rem;
    }
  }
}
.ENG{
  .in_pdWindow_item_description_w{
    align-items: normal;
    .indexfav{
      padding-top: 5px;
    }
  }
  .in_pdWindow_item_title {
    font-size: 1.2rem;
    width: 100%;
    margin: 0 auto;
    text-align: left;
    color: #4d4d4d;
    // line-height: 25px;
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    word-break: break-word;
    // margin-top: .5rem;
    // margin-bottom: .5rem;
    font-family: 'Domine-Regular' !important;
}
.productBox .more {
    width: 20rem;
    height: 50px;
    background: url(/images/pc/index_66.png) no-repeat center center;
    margin: 0 auto;
    background-size: contain;
    text-align: center;
    margin-top: 14px;
    a{
      font-family: 'Domine-Bold' !important;

    }
}
}
</style>
