<template>
  <div id="container" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <!-- <div class="ProducBanner">
      <ProductListSwiper
        class="innerBanner"
        :TitleName="$t('product.Producttitle')"
      />
    </div> -->
    <div class="ProductSearch">
      <div class="SearchSlide">
        <div class="bg" @click="closeSub"></div>
        <div class="leftSide">
          <advancedSearch
            @advancedChange="advancedChange"
            v-if="isAdvanced"
            @closeSub="closeSub"
            @resetAll="resetAll"
          />
        </div>
      </div>
      <div class="selectBar fix">
        <div class="slidebox" @click="showSearchSlide">
          <!-- <span class="el-icon-s-operation"></span> -->
          <b>{{ $t("product.productclassification") }}</b>
        </div>
        <div class="sortbox">
          <p class="sortTitle" @click="showList=!showList">
            {{$t('product.Paixu')}}
          </p>
          <transition name="el-fade-in-linear">
            <ul class="sortList" v-if="showList">
              <li @click="handleCommand('desc')" :style="{'color':sort=='desc'?'#b59e72':'#999999'}">{{$t('product.PriceHL')}}</li>
              <li @click="handleCommand('asc')" :style="{'color':sort=='asc'?'#b59e72':'#999999'}">{{$t('product.PriceLH')}}</li>
            </ul>
          </transition>
        </div>

        <!-- <div class="Sortlist">
              <div class="input" @click="openValue">
                <input
                  v-model="Character"
                  type="text"
                  :placeholder="$t('product.Paixu')"
                  disabled
                />
              </div>
              <div class="list" v-show="show" @change="getselect(PriceItem)">
                <ul>
                  <li>
                    <a
                      href="javascript:;"
                      @click="Passvalue('desc', $t('product.PriceHL'))"
                      >{{ $t("product.PriceHL") }}</a
                    >
                  </li>
                  <li>
                    <a
                      href="javascript:;"
                      @click="Passvalue('asc', $t('product.PriceLH'))"
                      >{{ $t("product.PriceLH") }}</a
                    >
                  </li>
                </ul>
              </div>
            </div> -->
        <!-- <ul>
          <li @click="showSearchSlide">
            <span class="el-icon-s-operation"></span
            ><b>{{ $t("product.Screening") }}</b>
          </li>
          <li style="width: 810px;border: none;">
            {{ $t("product.Total") }} {{ totalRecord }}
            {{ $t("product.Product") }}
          </li>
          <li style="width: 140px;">
            <select v-model="PriceItem" @change="getselect(PriceItem)">
              <option value="">{{ $t("product.Paixu") }}</option>
              <option value="desc">{{ $t("product.PriceHL") }}</option>
              <option value="asc">{{ $t("product.PriceLH") }}</option>
            </select>
          </li>
        </ul> -->
      </div>
      <!-- <advancedSearch :attrType="2"  @advancedChange="advancedChange" /> -->
      <transition name="slide">
        <div key="1" v-if="!waiting">
          <div class="prolist-box" v-if="proList.length > 0">
            <ins-productList :column="4" :allItems="proList" />
            <div class="pager" v-if="totalRecord > pageSize">
              <InsPage
                :total="totalRecord"
                v-model="currentPage"
                :pageNum="pageSize"
                :currentPage = "currentPage"
              ></InsPage>
            </div>
          </div>
          <div class="prolist-box" v-else>
            <h3 class="nocontentTips">{{ $t("messageTips.NoContent") }}</h3>
          </div>
        </div>
      </transition>
      <transition name="slide">
        <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
      </transition>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import YouWouldLike from '@/model/youWouldLike';
import $ from 'jquery';
@Component({
  components: {
    InsProductList: () =>
      import(
        /* webpackChunkName: 'product' */ '@/components/business/pc/product/InsProductList.vue'
      ),
    advancedSearch: () =>
      import(
        /* webpackChunkName: 'product' */ '@/components/hkTasteBusiness/pc/product/InsAdvancedSearch.vue'
      ),
    InsPage: () =>
      import(
        /* webpackChunkName: 'product' */ '@/components/base/pc/InsPage.vue'
      ),
    ProductListSwiper: () =>
      import(
        /* webpackChunkName: 'product' */ '@/components/hkTasteBusiness/pc/product/HkProductListSwiper.vue'
      )
  }
})
export default class InsProductSearch extends Vue {
  proList: YouWouldLike[] = []; // 产品数据
  currentPage: number = 1; // 当前页
  pageSize: number = 16; // 每页显示条目个数
  totalRecord: number = 0; // 总条目数
  private tips: boolean = true;
  private LoadingInstance!: any;

  attrs: object[] = []; // 选中的产品属性数组
  searchCatalogs: number[] = []; // 选中的产品目录数组
  searchType: number = 1; // 搜索类型（0 => 叠加，1 => 筛选）
  isAdvanced: boolean = true;
  PriceItem: string = '';
  private waiting: boolean = true;
  private show: boolean = false;
  Character: string = '';
  searchPrice: number[] = []; // 选中的产品价格范围
  showList: boolean = false;
  sort: any = null;
  SortName: string = '';
  // 搜索关键词
  get searchKey() {
    let a = this.$route.params.key;
    if (a === '-') {
      return '';
    } else {
      return a;
    }
  }
  // 重置搜索
  resetAll() {
    // this.$router.push('/product/search/-');
    window.location.href = '/product/search/-';
    this.closeSub();
  }
  // 关闭筛选弹窗
  closeSub() {
    $('.SearchSlide').fadeOut();
    $('.leftSide').removeClass('closeBar');
    document.body.style.overflow = 'auto';
  }
  // 打开筛选弹窗
  showSearchSlide() {
    $('.SearchSlide').fadeIn();
    $('.leftSide').addClass('closeBar');
    document.body.style.overflow = 'hidden';
  }
  // 价格传值
  getselect(val) {
    this.PriceItem = val;
    this.productSearch();
  }
  openValue() {
    this.show = !this.show;
  }
  Passvalue(val, text) {
    console.log(val, '传值Passvalue');
    this.PriceItem = val;
    this.Character = text;
    this.productSearch();
    this.show = false;
  }
  // 产品高级搜索
  productSearch() {
    this.TShake(() => {
      this.$Api.product
        .search({
          Key: this.searchKey,
          PageInfo: {
            Page: this.currentPage,
            PageSize: this.pageSize,
            SortName: 'SalePrice',
            SortOrder: this.PriceItem
          },
          CatIdS: this.searchCatalogs,
          Attrs: this.attrs,
          Type: this.searchType,
          Prices: this.searchPrice,
          Reflesh: 1
        })
        .then(result => {
          this.proList = result.YouWouldLike;
          this.totalRecord = result.TotalRecord;
          this.waiting = false;
          this.$HiddenLayer();
        });
    }, 500);
  }
  delay = 0;
  TShake(fn, d) {
    if (!(fn instanceof Function)) return;
    let timeout = d || 200;
    if (this.delay === 0) {
      this.delay = setTimeout(fn, timeout);
    } else {
      clearTimeout(this.delay);
      this.delay = setTimeout(fn, timeout);
    }
  }
  advancedChange(Attrs, Catalogs, Pricevalue) {
    this.currentPage = 1;
    this.attrs = Attrs;
    this.searchCatalogs = Catalogs;
    this.searchPrice = Pricevalue;
    console.log(Pricevalue, 'PricevaluePricevalue');
    this.productSearch();
  }
  handleCommand(sort) {
    this.sort = sort;
    if (sort === 'newest') {
      this.SortName = 'createdate';
      this.PriceItem = 'desc';
    } else {
      this.SortName = 'SalePrice';
      this.PriceItem = sort;
    }
    this.showList = false;
    // history.pushState(null, '', this.$route.path + '?attrs=' + JSON.stringify(this.attrs) + '&catalogs=' + JSON.stringify(this.searchCatalogs) + '&sort=' + this.sort + '&type=1');
    this.productSearch();
  }

  mounted() {
    // this.productSearch();
  }

  @Watch('searchKey', { deep: true })
  onSearchKeyChange() {
    this.productSearch();
  }

  @Watch('currentPage', { deep: true })
  onCurrentPage() {
    this.productSearch();
  }
}
</script>

<style lang="less">
.prolist-box {
  .pager {
    text-align: center;
    margin: 60px 0;
    button,
    li {
      margin: 0 6px !important;
      // border: 1px solid #e5e5e5;
      width: 50px;
      height: 50px;
      padding: 0 !important;
      display: inline-flex;
      justify-content: center;
      align-items: center;
      box-sizing: border-box;
      min-width: unset;
      border-radius: 5px;
      background-color: #f0f0f0;
    }

    li {
      font-family: "SourceHanSansSC-Regular";
      font-size: 20px;
      color: #333333;
      font-weight: normal;
      border-left: 0;
      &.active {
        background-color: #95d303;
        color: #fff;
      }

      &:last-child {
        border-right: 0;
      }
    }

    .el-pagination button {
      background-color: #95d303;

      .el-icon {
        font-size: 20px;
        color: #fff;
      }

      &:disabled {
        background-color: #f0f0f0;
        .el-icon {
          color: #333333;
        }
      }
    }
  }
}
.ENG{
  .selectBar {
    .slidebox b{
      font-size: 18px;
      font-family: 'Domine-Bold' !important;
    }
    .input input{
      font-family: 'Domine-Bold' !important;
    }
    .list > ul > li a{
      font-size: 16px !important;
    }
  }
  .InsAdvancedSearch .resetTitle{
    font-family: 'Domine-Bold' !important;
  }
  .InsAdvancedSearch .resetTitle span{
    font-family: element-icons !important;
  }
  .InsAdvancedSearch .priceRange .priceName{
    font-size: 20px;
    font-family: 'Domine-Bold' !important;
    color: #333333;
  }
  .selectBar .input input{
    font-size: 16px !important;
  }
  .selectBar .slidebox b{
    font-size: 16px !important;
  }
  .selectBar .sortList li{
    font-size: 16px !important;
  }
  .sortBox .sortTitle{
    font-size: 16px !important;
    font-family: 'Domine-Bold' !important;
  }
  .selectBar .sortbox .sortTitle{
    font-size: 16px !important;
    font-family: 'Domine-Bold' !important;
  }
}
</style>
<style scoped lang="less">
.nocontentTips {
  width: 95%;
  margin: 0 auto;
  font-size: 1.2rem;
  font-weight: 500;
  text-align: left;
  display: block;
  padding: 1rem;
  color: #666;
}
.faker {
  width: 100%;
  height: 29vw;
  background-color: aliceblue;
}
.ProducBanner {
  width: 100%;
  background-size: 100% 100%;
  display: inline-block;
  box-sizing: border-box;
  .innerBanner {
    width: 100%;
    margin: 0 auto;
    img {
      width: 100%;
    }
  }
}
.ProductSearch {
  .InsAdvancedSearch {
    background: transparent;
    height: 100vh;
    // overflow-y: scroll;
    // border-top-left-radius: 8px;
  }
}
.SearchSlide {
  width: 100%;
  position: fixed;
  left: 0;
  top: 0px;
  bottom: 0px;
  // background: rgba(0, 0, 0, 0.6);
  z-index: 999999;
  display: none;
  > .bg {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    background: rgba(0,0,0,.6);
  }
  .leftSide {
    width: 360px;
    left: -360px;
    min-height: 100%;
    position: absolute;
    transition: all 0.5s;
    background: #f2f1f0;
    border-top-right-radius: 8px;
  }
}
.ProductSearch {
  width: 1200px;
  margin: 50px auto 50px;

  .prolist-box {
    .pager {
      text-align: center;
      margin: 60px 0;
    }
  }
}
.banner {
  position: relative;
  img {
    width: 100%;
  }
  .titlename {
    position: absolute;
    top: 40%;
    width: 100%;
    margin: 0 auto;
    text-align: center;
    span {
      width: 1200px;
      margin: 0 auto;
      display: block;
      color: #fff;
      font-size: 28px;
      letter-spacing: 10px;
    }
  }
}
.closeBar {
  left: 0% !important;
}
.selectBar {
  width: 100%;
  margin: 0 auto;
  display: inline-block;
  margin-top: 2rem;
  // ul {
  //   width: 100%;
  //   margin: 0 auto;
  // }
  // li {
  //   float: left;
  //   margin-right: 4%;
  //   border: 1px solid #eee;
  //   height: 40px;
  //   line-height: 40px;
  //   list-style: none;
  //   display: flex;
  //   justify-items: center;
  //   justify-content: center;
  //   align-items: center;
  //   span {
  //     width: 20%;
  //     display: inline-block;
  //     font-size: 20px;
  //     text-align: center;
  //     color: #909399;
  //   }
  //   b {
  //     width: 60%;
  //     display: inline-block;
  //     text-align: center;
  //     font-size: 16px;
  //     font-weight: 500;
  //     color: #333333;
  //   }
  //   select {
  //     width: 100%;
  //     border: none;
  //     padding-left: 0.5rem;
  //     height: 40px;
  //     line-height: 40px;
  //     font-size: 14px;
  //     -webkit-appearance: none;
  //     -moz-appearance: none;
  //     appearance: none;
  //     background: url(/images/mobile/arrow-down-back.png) 90% 17px no-repeat;
  //     background-size: auto;
  //     outline: none;
  //     cursor: pointer;
  //   }
  //   &:last-child {
  //     margin-right: 0px !important;
  //     background: #fff !important;
  //     color: #333333;
  //     cursor: pointer;
  //   }
  //   &:first-child {
  //     width: 140px;
  //     cursor: pointer;
  //   }
  // }
  .slidebox{
    width: 254px;
    height: 50px;
    background: url('/images/pc/index_66.png') no-repeat center center;
    text-align: center;
    float: left;
    cursor: pointer;
    b{
      font-size: 18px;
      // font-family: 'SourceHanSerifCN-Bold';
      line-height: 50px;
      color: rgb(149,126,82);
    }
  }
  .Sortlist{
    float: right;
    width: 254px;
    height: 50px;
    background: url('/images/pc/index_66.png') no-repeat center center;
    text-align: center;
    position: relative;
    cursor: pointer;
  }
  .input {
        width: 100%;
        height: 40px;
        line-height: 40px;
        padding-left: 0;
        border: none;
        position: relative;
        color: #fff !important;
        margin: 0;
        box-sizing: border-box;
      }
      .input input {
        border: none;
        outline: none;
        width: 194px;
        background-color: rgba(255, 255, 255, 1) !important;
        background: rgba(255, 255, 255, 1) !important;
        font-size: 18px;
        color: rgb(149,126,82);
        // padding-left: 20px;
        height: 39px;
        line-height: 39px;
        box-sizing: border-box;
        text-align: center;
        border-radius: 0;
        cursor: pointer;
        font-weight: bold;
        margin-top: 6px;
      }
      input::-webkit-input-placeholder {
        color: rgb(149,126,82);
        font-size: 18px;
        background-color: rgba(255, 255, 255, 1) !important;
      }
      /* Mozilla Firefox 4 to 18 */
      input:-moz-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 18px;
        background-color: rgba(255, 255, 255, 1) !important;
      }
      /* Mozilla Firefox 19+ */
      input::-moz-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 18px;
        background-color: rgba(255, 255, 255, 1) !important;
      }
      /* Internet Explorer 10+ */
      input:-ms-input-placeholder {
        color: rgb(149,126,82);
        font-size: 18px;
        background-color: rgba(255, 255, 255, 1) !important;
      }
      input::-webkit-input-placeholder, textarea::-webkit-input-placeholder { color: rgb(149,126,82); } input:-moz-placeholder, textarea:-moz-placeholder { color: rgb(149,126,82); } input::-moz-placeholder, textarea::-moz-placeholder { color: rgb(149,126,82); } input:-ms-input-placeholder, textarea:-ms-input-placeholder { color: rgb(149,126,82); }
      input:disabled{
      color: rgb(149,126,82) !important;
    }
      .input img {
        position: absolute;
        left: 14px;
        top: 50%;
        transform: translateY(-50%);
        width: 14px;
        display: block;
      }
      .list {
        width: 200px;
        // border: 1px solid #666666;
        overflow: hidden;
        position: absolute;
        z-index: 1000;
        background-color: #fff;
        cursor: pointer;
        top: 60px;
        box-sizing: border-box;
        left: 50%;
        transform: translateX(-50%);
        box-shadow: 0 0 5px #eeedec;
      }
      .list > ul > li {
        width: 100%;
        height: 40px;
        cursor: pointer;
        line-height: 40px;
        padding-left: 0;
        border-bottom: 1px solid #ebebeb;
        background-color: #ffffff;
        text-align: center;
        &:last-child {
          border-bottom: none;
        }
        a {
          color: #999999;
          font-size: 18px;
          text-decoration: none;
        }
      }
      .list > ul > li:hover {
        // background-color: rgba(181, 158, 114, 1);
        a {
          color: rgba(181, 158, 114, 1);
        }
      }
      .sortbox{
        float: right;
        width: 254px;
        height: 50px;
        background: url(/images/pc/index_66.png) no-repeat 50%;
        text-align: center;
        position: relative;
        cursor: pointer;
        .sortTitle{
          font-size: 18px;
          line-height: 50px;
          color: rgb(149,126,82) !important;
          cursor: pointer;
          font-weight: 700;
          font-family: 'SourceHanSerifCN-Bold';
        }
      }

  .sortList{
    width: 200px;
    overflow: hidden;
    position: absolute;
    top:60px;
    left: 50%;
    transform: translateX(-50%);
    box-sizing:border-box;
    background: #fff;
    border-radius:3px;
    box-shadow: 0 0 5px #eeedec;
    z-index: 1;
    li{
      font-size: 18px;
      height: 40px;
      cursor: pointer;
      line-height: 40px;
      padding-left: 0;
      border-bottom: 1px solid #ebebeb;
      background-color: #fff;
      &:last-of-type{
        border-bottom: none;
      }
      &:hover{
        color: rgb(149,126,82) !important;
      }
    }
  }
}
</style>
