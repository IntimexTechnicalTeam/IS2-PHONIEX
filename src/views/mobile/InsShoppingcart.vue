<template>
  <div class="shoppingcart_warrper" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <!--main-content-->
        <div class="shoppingcart_header">
          <div class="favorite-box-top">
            <div class="login-register-title">{{$t('Shoppingcart.ShoppingcartTitle')}}</div>
            <div class="clear"></div>
          </div>
        </div>
        <div class="ShoppingCartItem_warpper"  v-for="(one,index) in items" :key="index">
            <div class="shoppingcart_item_image">
                <a class="product-img" v-bind:href="'/product/Detail/'+one.Product.Sku">
                    <img v-bind:src="one.Product.Img_M" alt />
                  </a>
            </div>
            <div class="shoppingcart_item_detail">
              <div class="left">
                <div class="shoppingcart_item_name">{{one.Product.Name}}</div>
                <div class="shoppingcart_item_price">
                    <div>{{Currency.Code}} {{(one.Product.SalePrice) | PriceFormat}}</div>
                </div>
              </div>
              <div class="right">
                <div class="shoppingcart_item_qty">
                  <div class="common-num">
                      <a
                        class="reduce-num"
                        href="javascript:;"
                        v-on:click=" minusQty(one,one.Id,$event);"
                      >-</a>
                      <div class="num-content">
                        <input
                          class="input-text"
                          type="text"
                          data-num="1"
                          disabled
                          v-model="one.Qty"
                          v-on:change="updateQty(one,one.Id,$event)"
                        />
                      </div>
                      <a class="add-num" href="javascript:;" v-on:click="plusQty(one,one.Id,$event);" :class="{'disabled':one.IsAdd}">+</a>
                      </div>
                </div>
              </div>
                <!-- <div class="shoppingcart_item_code">{{one.Product.Code}}</div> -->
                <!-- <div class="shoppingcart_item_attr">
                      <span v-if="one.AttrName1">{{one.AttrTypeName1}}：{{one.AttrName1}}</span>&nbsp;
                      <span v-if="one.AttrName2">{{one.AttrTypeName2}}：{{one.AttrName2}}</span>&nbsp;
                      <span v-if="one.AttrName3">{{one.AttrTypeName3}}：{{one.AttrName3}}</span>&nbsp;
                </div> -->

            </div>
            <div class="close"  v-on:click="removeItem(index)">
                <i class="el-icon-error"></i>
            </div>
        </div>
        <div>
          <div class="shoppingcart_total1">
            <span>{{$t('Order.CartSubtotal')}}: </span>
            <span class="total-price">
              {{Currency.Code}} {{(totalAmount) | PriceFormat}}
            </span>
          </div>
          <div class="shoppingcart_total"><ElButton type="success" @click="submit"><span style="font-size:1.5rem;">{{ $t('Shoppingcart.Checkout') }}</span></ElButton></div>
        </div>
    <!--main-content-->
  </div>
</template>
<script lang='ts'>
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import ShopCart from '../../model/ShopCart';
import ShopCartItem from '../../model/shopCartItem';
import Currency from '../../model/currency';
import { Button as ElButton } from 'element-ui';
class Update {
  itemId!: string;
  qty!: number;
  constructor (itemId:string, qty:number) {
    this.itemId = itemId;
    this.qty = qty;
  }
}
@Component({ components: { ElButton } })
export default class InsShoppingcart extends Vue {
  private ShoppingCart:ShopCart = new ShopCart();
  prodcutSrc: string = require('@/assets/Images/270_b.jpg');
  step: number = 1;
  totalAmount: number = 0;
  // itemsAmount: number = 0;
  // ItemsTaxAmount: number = 0;
  Currency: Currency = new Currency();
  MaxQty: number = 20;
  // cartItems: any[] = [];
  // currentCode: any = '';
  items: any[] = [
  ];
  itemQty:number=0;
  private UpdateQueQue:Update[] = [];
  isAdd:boolean = false;
  mounted () {
    // this.loadItems();
  }
  created () {
    this.load().then(() => { this.$HiddenLayer(); });
  }
  load () {
    let load = this.$Api.shoppingCart.getShoppingCart().then((result) => {
      this.ShoppingCart = result.ShopCart;
      this.Currency = result.ShopCart.DefaultCurrency;
      this.items = result.ShopCart.Items;
      this.items.forEach(v => {
        this.$set(v, 'IsAdd', false);
      });
      if (this.ShoppingCart.Items.length === 0) this.$Confirm(this.$t('Message.Message'), this.$t('Shoppingcart.None'), () => { this.$router.push('/product/search/-'); }, () => { this.$router.push('/'); });
    });
    this.loadItems();
    return load;
  }
  loadItems () {
    var _this = this;
    var itemsprice = 0;
    var Currencys;
    var itemQ;
    _this.items.forEach(element => {
      itemsprice += element.Product.SalePrice * element.Qty;
      Currencys = element.Product.Currency.Code;
      itemQ = element.Qty;
    });
    // _this.Currency = Currencys;
    _this.totalAmount = itemsprice;
    _this.itemQty = itemQ;
  }
  @Watch('items', { deep: true })
  onItemsChange (o, n) {
    this.loadItems();
  }
  removeItem (one) {
    // this.loadItems();
    let item:ShopCartItem = this.items.splice(one, 1)[0];
    this.$Api.shoppingCart.removeItem(item.Id).then(result => {
      this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
      if (this.ShoppingCart.Items.length === 0) this.$Confirm(this.$t('Message.Message'), this.$t('Shoppingcart.None'), () => { this.$router.push('/product/search/-'); }, () => { this.$router.push('/'); });
    });
  }
  next () {
    // if (!this.items || this.items.length === 0) {
    //     showInfo('<%=Resources.Caption.CartEmpty%>');
    //     return false;
    // }
  }
  minusQty (one, id, event) {
    let _this = this;
    one.Qty--;
    if (one.Qty < 1) {
      one.Qty = 1;
    }
    this.Shake(() => {
      this.$Api.shoppingCart.updateItemQty(id, one.Qty).then((result) => {
            this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
            one.IsAdd = false;
      });
    }, 500);
  }
  plusQty (one, id, event) {
    var a = one.Qty;
    let _this = this;
    one.Qty++;
    if (one.IsAdd === false) {
        one.IsAdd = true;
      setTimeout(() => {
             _this.$Api.shoppingCart.updateItemQty(id, one.Qty).then((result) => {
               if (result.Message.Succeeded) {
                 _this.$store.dispatch('setShopCart', this.$Api.shoppingCart.getShoppingCart());
                 one.IsAdd = false;
               } else {
                 one.Qty = a;
                  this.$message({
                      message: result.Message.Message as string,
                      type: 'success',
                      customClass: 'messageboxNoraml'
                });
               }
            });
      }, 500);
    }
  }
  updateQty (one, id, event) {
    let _this = this;
    if (one.Qty < 1) {
      one.Qty = 1;
    }
    // if (one.Qty >= this.MaxQty) {
    //   one.Qty = this.MaxQty;
    // }
  }
  submit () {
    let temp = {};
    let item:Update;
    let waittingList = [Promise.resolve('head')];
    while (this.UpdateQueQue.length !== 0) {
      item = this.UpdateQueQue.shift() as Update;
      temp[item.itemId] = item.qty;
    }
    Object.keys(temp).forEach((element) => {
      waittingList.push(this.$Api.shoppingCart.updateItemQty(element, temp[element]));
    });
    Promise.all(waittingList).then((result) => {
      // this.$router.push('/account/checkout');
      if (this.$Storage.get('isLogin') === 1) this.$router.push('/account/checkout');
      else {
        // this.$Login(() => { this.$ShowLayer(); this.load().then(() => { this.$HiddenLayer(); }); });
        this.$router.push('/account/login?returnurl=/account/checkout');
      }
    });
  }
}
</script>
<style lang="less">
.ShoppingCartItem_warpper .el-input-number {
    position: relative;
    display: inline-block;
    width: 8rem;
    border: none;
}
</style>
<style scoped lang='less'>
/*我的最爱*/
  .disabled {
      pointer-events: none;
      filter: alpha(opacity=50); /*IE滤镜，透明度50%*/
      -moz-opacity: 0.5; /*Firefox私有，透明度50%*/
      opacity: 0.5; /*其他，透明度50%*/
  }
#main-content {
  width: 90%;
  margin: 0 auto;
}
/*购物流程*/

.ShoppingCartItem_warpper{
    display: flex;
    flex-wrap: nowrap;
    padding-top: 1rem;
    padding-bottom: 1rem;
    border-bottom: 1px solid #eee;
    position: relative;
    .close{
        position: absolute;
        top: 50%;
        right: 0.5rem;
        transform: translateY(-50%);
        i{
            font-size: 2rem;
            color: #cccccc;
        }
    }
}
.shoppingcart_item_image{
    margin: 0 0 0 1rem;
}
.shoppingcart_item_image img{
    width: 8rem;
    height: 8rem;
    border-radius: 3px;
}
.shoppingcart_item_detail{
    margin: 0 0 0 0.5rem;
    // margin-right: 3.5rem;
    display: flex;
    width: 100%;
    align-items: center;
    .left{
      width: 50%;

    }
    .right{
      width: 50%;
    }
}
.shoppingcart_item_name,
.shoppingcart_item_code,
.shoppingcart_item_attr,
.shoppingcart_item_price{
    line-height: 2rem;
    font-size: 1.2rem;
    // width: 10rem;
}
.shoppingcart_item_name{
    overflow: hidden;
    display: -webkit-box;
    -webkit-line-clamp: 1;
    -webkit-box-orient: vertical;
    word-break: break-word;
    font-family: 'SourceHanSerifCN-Bold';
}
.shoppingcart_item_price >div{
    font-size: 1.2rem;
    color:#b59e72;
}
.shoppingcart_item_qty{
    display: flex;
    .qty_count{
        line-height: 26px;
        font-size: 1.2rem
    }
}
.shoppingcart_item_attr{
    span{
        margin-right: 1rem;
    }
}
.shoppingcart_item_price{
    display: flex;
    flex-wrap: nowrap;
    justify-content: space-between;
}
.shoppingcart_warrper{
    background-color: white;
    padding: 2rem 0;
    border-radius: .5rem;
    // min-height: calc(100vh - 402px);
    .shoppingcart_header{
        margin-bottom: 2rem;
        margin-top: 1rem;
        .favorite-box-top{
          width: 16rem;
          height: 4rem;
          background: url(/images/pc/index_66.png) no-repeat center center;
          background-size: contain;
          text-align: center;
          margin: 0 auto;

        }
        .login-register-title {
          line-height: 4rem;
          text-align: center;
          color: #b59e72;
          font-size: 1.4rem;
          font-weight: bold;
        }
    }
}
.shoppingcart_total{
    text-align: right;
    padding: 1rem;
    /deep/ .el-button{
      width: 100%;
    }
}
.shoppingcart_total1{
    font-size: 1.5rem;
    text-align: right;
    padding: 1rem;
    color:#d92526;
    span{
      font-size: 1.5rem;
      color: #b59e72;
    }
}
.num-content{
    float: left;
}
.num-content .input-text {
      display: block;
    width: 36px;
    height: 32px;
    line-height: 30px;
    text-align: center;
    border: none;
    color: #666666;
    outline: none;
    /* border-left: 1px solid #b59e72; */
    /* border-right: 1px solid #b59e72; */
    box-sizing: border-box;
}
.common-num {
  display: inline-block;
  border: 1px solid #b59e72;
  border-radius: 3px;
}
.common-num a{
    float: left;
    width: 30px;
    height: 32px;
    line-height: 30px;
    text-align: center;
    font-size: 20px;
    color: #fff;
    background-color: #b59e72;
}
.ENG.shoppingcart_warrper{
  .shoppingcart_item_name{
    -webkit-line-clamp: 3;
    line-height: 1.6rem;
    margin-bottom: 0.5rem;
  }
  .common-num a{
    line-height: 36px;
  }
}
</style>
