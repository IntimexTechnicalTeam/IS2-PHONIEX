<template>
<div class="in_panel_header" :class="{'ENG' : $Storage.get('locale') === 'E'}">
  <div class="in_panel_header_w fix">
    <div class="in_panel_header_left">
      <div class="in_panel_subTitle">{{panelDetail.Name}}</div>
      <div class="in_panel_subTitle"><inPrices :primePrices="panelDetail.ListPrice+AddPrice" :currentPrices="panelDetail.SalePrice+AddPrice"  :currency="panelDetail.Currency" :DefaultListPrice="panelDetail.DefaultListPrice+AddPrice" :DefaultSalePrice="panelDetail.DefaultSalePrice+AddPrice" :DefaultCurrency="panelDetail.DefaultCurrency" size="huge" :heightLine="true" :max="panelDetail.MaxPurQty" :min="panelDetail.MinPurQty"></inPrices></div>
      <Rate  v-model="panelDetail.Score" disabled  disabled-void-color="#5f6548" disabled-void-icon-class="el-icon-star-off"></Rate>
    </div>
    <div class="in_panel_header_right">
      <div class="ProductCode">
        {{$t("product.ProductCode")}}: {{panelDetail.Code}}
      </div>
      <div class="in_pannel_addtofav"><img :src="panelDetail.IsFavorite ? '/images/pc/index_42hover.png': '/images/pc/index_42.png'" @click="addFavorite"/> {{$t('product.collect')}}</div>
      <HkProductShare></HkProductShare>
    </div>
  </div>
  <div class="in_panel_header_w introduction" v-html="panelDetail.OverView"></div>
    <!-- <div class="in_unitInfo" v-if="panelDetail.UnitInfo.Desc!==null">{{$t('product.Unit')}}:{{panelDetail.UnitInfo.Desc}}</div>
    <div class="in_panel_product">
        <div class="ProductCode">
            <div class="leftpart">{{$t("product.ProductCode")}}: {{panelDetail.Code}}</div>
            <div class="rightpart">{{$t("Action.Share")}}:<HkProductShare></HkProductShare></div>
        </div>
    </div> -->
</div>
</template>
<script lang="ts">
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import PanelDetail from '@/model/PanelDetail';
import inPrices from '@/components/base/mobile/InsPrices.vue';
import HkProductShare from '@/components/hkTasteBusiness/mobile/product/HkProductShare.vue';
import { Rate } from 'element-ui';
@Component({ components: { inPrices, HkProductShare, Rate } })
export default class PkProductInfo extends Vue {
  @Prop() private readonly panelDetail!: PanelDetail;
  @Prop() private readonly ProductSku!: string;
  @Prop() private readonly AddPrice!: string;
  private Score:number=0;
  addFavorite () {
    let ps;
    if (this.panelDetail.IsFavorite) {
      ps = this.$Api.product.removeFavorite(this.ProductSku).then((result) => {
        if (result.Succeeded) {
            this.$message({
              message: this.$t('product.successInRemoving') as string,
              type: 'success',
              customClass: 'messageBoxMobile'
            });
          this.panelDetail.IsFavorite = false;
        } else if (result.ReturnValue.Code === 1000) {
          Vue.prototype.$Confirm(this.$t('product.logouted'), this.$t('product.loginow'), () => { this.$Login(this.addFavorite); });
        }
      });
    } else {
      ps = this.$Api.product.addFavorite(this.ProductSku).then((result) => {
        if (result.Succeeded) {
          // Vue.prototype.$Confirm(this.$t('Message.Message'), this.$t('product.successInAdding'));
          this.$message({
            message: this.$t('product.successInAdding') as string,
            type: 'success',
            customClass: 'messageBoxMobile'
          });
          this.panelDetail.IsFavorite = true;
        } else if (result.ReturnValue.Code === 1000) {
          Vue.prototype.$Confirm(this.$t('product.logouted'), this.$t('product.loginow'), () => { this.$Login(this.addFavorite); });
        }
      });
    }
    return ps;
  }
}
</script>
<style lang="less">
.messageBoxMobile{
      z-index: 100000!important;
}
</style>
<style scoped lang="less">
.in_unitInfo{
  font-size: 1.2rem;
  color:@base_color;
  text-align: right;
  width: 95%;
  margin: 0 auto;
}
.in_panel_header{
  width: 100%;
  display: block;
  .in_panel_header_w{
    width: 94%;
    margin: 0 auto;
    .in_panel_header_left{
      width: 70%;
      float: left;
      /deep/ .el-rate{
        .el-rate__icon{
          font-size: 26px;
          color: #ffbb07 !important;
        }
        .el-icon-star-off{
          font-size: 23px;
          color: #ffbb07 !important;
        }
      }
    }
    .in_panel_header_right{
      width: 30%;
      float: right;
      text-align: right;
      .ProductCode{
        font-size: 1.2rem;
        color: #999999;
        margin-top: 0.5rem;
        margin-bottom: 0.5rem;
      }

    }
  }
  .introduction{
    margin-top: 1rem;
    /deep/ p{
      font-size: 1.2rem;
      color: #808080;
      line-height: 1.8rem;
      text-align: justify;
      word-break: keep-all;
    }
  }
}
.in_panel_product{
    width: 100%;
    padding-bottom: 3rem;
    border-bottom: 1px solid #505050;
    padding-top: 3rem;
}
.in_pannel_addtofav{
    // width: 95%;
    // margin: 0 auto;
    // text-align: center;
    display: flex;
    justify-content: right;
    align-items: center;
    color: #666666;
    font-size: 1.2rem;
    margin-top: 1rem;
    margin-bottom: 1rem;
}
.in_pannel_addtofav img{
    // width:2.5rem;
    margin-right: 0.5rem;
    display: block;
}
.in_panel_product .ProductCode{
    width: 95%;
    margin: 0 auto;
}
.in_panel_product .ProductCode .leftpart{
    width:50%;
    float: left;
    font-size: 1.2rem;
    word-break: break-all;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    padding-top: .5rem;
}
.in_panel_product .ProductCode .rightpart{
    width: 50%;
    float: left;
    text-align: right;
    display: flex;
    align-items: center;
    justify-content: flex-end;
    font-size: 1rem;
}
.in_panel_subTitle{
    font-size: 2rem;
    position: relative;
    width: 100%;
    margin: 0 auto;
    text-align: left;
    font-family: 'SourceHanSerifCN-Bold';
    color: #4d4d4d;
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    word-break: break-word;
    margin-bottom: 0.5rem;
    // margin: 0 auto;
    // text-align: center;
    // display: flex;
    // align-items: center;
    // justify-content: center;
  >img{
    position: absolute;
    right: 0;
    top: 50%;
    transform: translate(0,-50%);
  }
}
.ENG{
  .in_panel_subTitle{
    font-size: 1.6rem;
    line-height: 2.4rem;
    font-family: 'Domine-Bold';
  }
}
</style>
