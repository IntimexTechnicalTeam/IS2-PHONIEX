<template>
<div class="in_panel_header">
  <div class="in_panel_header_w fix">
    <div class="in_panel_header_left">
      <div class="p-name-box">
        <p style="width: 88%;">{{panelDetail.Name}}</p>

      </div>
      <div class="in_panel_subTitle">
        <inPrices :primePrices="panelDetail.ListPrice+AddPrice" :currentPrices="panelDetail.SalePrice+AddPrice"  :currency="panelDetail.Currency" :DefaultListPrice="panelDetail.DefaultListPrice+AddPrice" :DefaultSalePrice="panelDetail.DefaultSalePrice+AddPrice" :DefaultCurrency="panelDetail.DefaultCurrency" size="huge" :heightLine="true" :max="panelDetail.MaxPurQty" :min="panelDetail.MinPurQty"></inPrices>
      </div>
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
  <div class="introduction" v-html="panelDetail.OverView">
  </div>
    <!-- <div class="in_unitInfo" v-if="panelDetail.UnitInfo.Desc!==null">{{$t('product.Unit')}}:{{panelDetail.UnitInfo.Desc}}</div>
    <div class="in_panel_product">
        <div class="ProductCode">
            <div class="leftpart">{{$t("product.ProductCode")}}: {{panelDetail.Code}}</div>
            <div class="rightpart"><HkProductShare></HkProductShare></div>
        </div>
    </div> -->
</div>
</template>
<script lang="ts">
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import PanelDetail from '@/model/PanelDetail';
import inPrices from '@/components/base/pc/InsPrices.vue';
import HkProductShare from '@/components/hkTasteBusiness/pc/product/HkProductShare.vue';
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
        // Vue.prototype.$Confirm(this.$t('Message.Message'), this.$t('product.successInRemoving'));
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
    // return this.$Api.product.addFavorite(this.ProductSku).then((result) => {
    //   if (result.Succeeded) {
    //     // Vue.prototype.$Confirm('succeed', this.$t('product.successInAdding'));
    //   } else if (result.ReturnValue.Code === 1000) {
    //     Vue.prototype.$Confirm(this.$t('product.logouted'), this.$t('product.loginow'), () => { this.$Login(this.addFavorite); });
    //   }
    // });
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
  font-size: 16px;
  color:@base_color;
  text-align: right;
}
.p-name-box {
    width: 100%;
    position: relative;
    // margin-top: 10px;
}
.p-name-box p {
    width: 100%;
    color: #4d4d4d;
    font-size: 26px;
    line-height: 26px;
    overflow: hidden;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    word-wrap: break-word;
    text-align: left;
    font-family: 'SourceHanSerifCN-Bold';
}
.p-name-box .in_pannel_addtofav{
    position: absolute;
    right: 30px;
    top: 0;
    display: block;
    width: 30px;
    height: 37px;
}
.p-name-box .in_pannel_addtofav img{
    width: 30px;
    cursor: pointer;
}
.in_panel_header{
  width: 100%;
  display: block;
  .in_panel_header_w{
    margin-bottom: 20px;
  }
  .in_panel_header_left{
    float: left;
    width: 80%;
  }
  .in_panel_header_right{
    float: right;
    width: 20%;
    text-align: right;
    .ProductCode{
      color: #999999;
      font-size: 16px;
    }
    .in_pannel_addtofav{
      display: flex;
      align-items: center;
      justify-content: right;
      font-size: 16px;
      margin-top: 10px;
      color: #666666;
      img{
        margin-right: 10px;
      }
    }
  }
  .introduction{
    min-height: 224px;
    /deep/ p{
      color: #808080;
      line-height: 30px;
      font-size: 16px;
    }
  }
  /deep/ .el-rate{
    height: 30px;
    .el-rate__icon{
          font-size: 30px;
    }
    .el-icon-star-off{
        font-size: 24.5px;
        color: #ffbb07 !important;
    }
  }
}
.in_panel_product{
    width: 100%;
    padding-bottom: 40px;
    border-bottom: 1px solid #505050;
    padding-top: 30px;
    margin-bottom: 10px;
}

.in_panel_product .ProductCode{
    width: 100%;
    display: block;
}
.in_panel_product .ProductCode .leftpart{
    width: 60%;
    float: left;
    font-size: 14px;
    word-break: break-all;
    display: -webkit-box;
    display: -ms-flexbox;
    display: flex;
    -webkit-box-align: center;
    -ms-flex-align: center;
    align-items: center;
    padding-top: 0.5rem;
}
.in_panel_product .ProductCode .rightpart{
    width: 40%;
    float:left;
}
.in_panel_subTitle{
    font-size: 16px!important;
    position: relative;
    width: 88%;
    text-align: left;
    display: flex;
    align-items: center;
    justify-content: left;
    margin-top: 15px;
    margin-bottom: 10px;
  >img{
    position: absolute;
    right: 0;
    top: 50%;
    transform: translate(0,-50%);
  }
}
</style>
