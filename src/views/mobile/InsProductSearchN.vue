<template>
  <div id="container" class="ProductSearch" :class="{'ENG' : $Storage.get('locale') === 'E'}">
        <!-- <div class="ProducBanner">
            <ProductListSwiper :TitleName="$t('product.Producttitle')"/>
        </div> -->
        <div class="SearchSlide fix">
          <div class="bg" @click="closeSub"></div>
          <div class="leftSide">
            <advancedSearch @advancedChange="advancedChange" v-if="isAdvanced"  @closeSub="closeSub" @resetAll="resetAll" />
          </div>
        </div>
      <div class="selectBar fix">
          <ul>
            <li @click="showSearchSlide">
              <!-- <span class="el-icon-s-operation"></span> -->
              <b>{{$t('product.productclassification')}}</b>
            </li>
            <li class="sortBox">
              <!-- <select v-model="PriceItem" @change="getselect(PriceItem)">
                <option value="desc">{{$t('product.PriceHL')}}</option>
                <option value="asc">{{$t('product.PriceLH')}}</option>
              </select> -->
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
            <p class="sortTitle" @click="showList=!showList">
                {{$t('product.Paixu')}}
              </p>
              <transition name="el-fade-in-linear">
              <ul class="sortList" v-if="showList">
                <li @click="handleCommand('desc')" :style="{'color':sort=='desc'?'#b59e72':'#999999'}">{{$t('product.PriceHL')}}</li>
                <li @click="handleCommand('asc')" :style="{'color':sort=='asc'?'#b59e72':'#999999'}">{{$t('product.PriceLH')}}</li>
                <!-- <li @click="handleCommand('newest')" :style="{'color':sort=='newest'?'#999999':'#b59e72'}">{{$t('product.Newest')}}</li> -->
              </ul>
              </transition>
            </li>
          </ul>
        </div>
    <!-- <advancedSearch :attrType="2"  @advancedChange="advancedChange" /> -->

    <div class="prolist-box fix">
      <div class="products_container" v-if="proList.length>0">
          <InsProductList v-for="item in proList" :key="item.productCode" :item="item" :needCode="false" class="product_item" ></InsProductList>
        </div>
        <div class="products_container" v-else>
             <h3 class="nocontentTips">{{$t('messageTips.NoContent')}}</h3>
        </div>
          <div ref="load" class="loading" @touchstart="loading" v-if="totalRecord>pageSize"><p>{{tips?$t('Action.LoadMore'):$t('home.Thatsall')}}</p></div>
          <div class="loading" v-else><p>{{$t('home.Thatsall')}}</p></div>
    </div>
  </div>
</template>
<script lang="ts">
import { Vue, Prop, Component, Watch } from 'vue-property-decorator';
import YouWouldLike from '@/model/youWouldLike';
import { Loading } from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';
import $ from 'jquery';
@Component({
  components: {
    InsProductList: () => import('@/components/hkTasteBusiness/mobile/product/HkProductWindow.vue'),
    advancedSearch: () => import('@/components/hkTasteBusiness/mobile/product/InsAdvancedSearch.vue'),
    ProductListSwiper: () => import('@/components/hkTasteBusiness/mobile/product/HkProductListSwiper.vue')
  }
})
export default class InsProductSearch extends Vue {
  proList: YouWouldLike[] = []; // 产品数据
  currentPage: number = 1; // 当前页
  pageSize: number = 20; // 每页显示条目个数
  totalRecord: number = 0;// 总条目数
  private tips:boolean = true;
  private LoadingInstance!: any;

  attrs: object[] = []; // 选中的产品属性数组
  searchCatalogs: number[] = []; // 选中的产品目录数组
  searchType: number = 1; // 搜索类型（0 => 叠加，1 => 筛选）
  PriceItem:string='';
  isAdvanced: boolean = true;
  private show: boolean = false;
  Character: string = '';
  searchPrice: number[] = []; // 选中的产品价格范围
  showList:boolean = false;
  SortName:string = '';
  sort:any = null;

  // 搜索关键词
  get searchKey () {
    let a = this.$store.state.searchKey;
    if (a === '-' || a === '') {
      return '';
    } else {
      return a;
    }
  }
  // 价格传值
  getselect (val) {
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
  productSearch (flag: string = '') {
    this.tips = true;
    this.$Api.product.search({
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
    }).then((result) => {
      if (flag === 'loadpage') {
        this.proList = this.proList.concat(result.YouWouldLike);
        this.totalRecord = result.TotalRecord;
      } else {
        this.proList = result.YouWouldLike;
        this.totalRecord = result.TotalRecord;
      }
      this.$HiddenLayer();
    });
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
    history.pushState(null, '', this.$route.path + '?attrs=' + JSON.stringify(this.attrs) + '&catalogs=' + JSON.stringify(this.searchCatalogs) + '&sort=' + this.sort + '&type=1');
    this.productSearch();
  }
  // 重置搜索
  resetAll () {
    window.location.href = '/product/search/-';
    this.closeSub();
  }
  // 关闭筛选弹窗
  closeSub () {
    $('.SearchSlide').fadeOut();
    $('.leftSide').removeClass('closeBar');
    document.body.style.overflow = 'auto';
  }
  // 打开筛选弹窗
  showSearchSlide () {
    $('.SearchSlide').fadeIn();
    $('.leftSide').addClass('closeBar');
    document.body.style.overflow = 'hidden';
  }
  loading (e) {
    if (this.tips) {
      this.LoadingInstance = Loading.service({
        target: this.$refs.load as any,
        fullscreen: false,
        // spinner: 'el-icon-loading',
        text: 'Loading...'
      });
      setTimeout(() => {
        this.load();
        this.LoadingInstance.close();
      }, 2000);
    }
  }
  load () {
    console.log(this.totalRecord, this.proList.length);
    if (this.totalRecord !== this.proList.length) { this.currentPage++; } else { this.tips = false; }
  }

  mounted () {

  }

  @Watch('searchKey', { deep: true })
  onSearchKeyChange () {
    this.productSearch();
  }

  @Watch('currentPage', { deep: true })
  onCurrentPage () {
    if (this.currentPage !== 1) {
      this.productSearch('loadpage');
    }
  }
}
</script>

<style lang="less">
.ProductSearch {
  .el-loading-spinner .circular {
    display: none;
  }

  .el-loading-text {
    font-size: 1.3rem;
    color: #cccccc;
    font-family: 'Arial';
  }

  .el-loading-parent--relative {
    > p {
      display: none;
    }
  }
}
.Sortlist{
  .input input {
    color: rgb(149,126,82) !important;
  }
}

</style>

<style scoped lang="less">
.nocontentTips{
  width: 95%;
  margin: 0 auto;
  font-size: 1.4rem;
  font-weight: 500;
  text-align: left;
  display: block;
  padding: 1rem;
  color: #666;
}
.product_warpper{
  width: 100%;
  margin:0 auto;
}
.products_container{
  display: flex;
  flex-wrap: wrap;
  width: 94%;
    margin: 0 auto;
}

.product_item{
    width: 50% !important;
    padding:2rem 0.5rem 0;
    padding-top: 1rem;
    padding-bottom: 1rem;
    box-sizing:border-box;
    &:nth-child(2n+1){
      padding-left: 0;
    }
    &:nth-child(2n){
      padding-right: 0;
    }
}

.loading{
    text-align: center;
    height: 3.5rem;
    line-height: 3.5rem;
    margin: 0 auto;
    margin-top: 2rem;
    margin-bottom: 2rem;
    background: url('/images/pc/index_66.png') no-repeat center center;
    background-size: contain;
    width: 50%;
    p{
      font-size: 1.2rem;
      color: #b59e72;
      font-weight: bold;
    }
}

.ProductSearch {
  .InsAdvancedSearch {
    background: #fff;
    min-height: 100vh;
  }
}
.SearchSlide{
  width: 100%;
  position: fixed;
  left: 0;
  top: 0px;
  bottom: 0px;
  // background: rgba(0,0,0,.6);
  overflow-x: scroll;
  z-index: 999999;
  display: none;
  .leftSide{
    width: 80%;
    left:-80%;
    min-height: 100%;
    position: absolute;
    transition: all .5s;
    background-color: #f2f1f0;
  }
  > .bg {
    position: absolute;
    width: 100%;
    height: 100%;
    left: 0;
    top: 0;
    background: rgba(0,0,0,.6);
  }
}
.closeBar{
    left: 0%!important;
  }
.selectBar{
    width: 94%;
    margin: 0 auto;
    // display: inline-block;
    margin-top: 4rem;
  > ul{
    width: 100%;
    margin: 0 auto;
    >li{
    float: left;
    margin-right: 2%;
    width: 49%;
    background: #FFF;
    // border:1px solid #eee;
    font-size: 1.2rem;
    background: url('/images/pc/index_66.png') no-repeat center center;
    background-size: contain;
    color: #FFF;
    height: 3.5rem;
    line-height: 3.1rem;
    list-style: none;
    position: relative;
    span{
    width: 20%;
    display: inline-block;
    font-size: 1.4rem;
    text-align: center;
    }
    b{
      width: 100%;
      display: block;
      text-align: center;
      font-size: 1.2rem;
      // font-weight: 500;
      color: rgb(149,126,82);
      font-family: 'SourceHanSerifCN-Bold';
    }
    select{
    width: 100%;
    border: none;
    padding-left: .5rem;
    height: 3.5rem;
    line-height: 3.5rem;
    font-size: 1.2rem;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    background: url(/images/mobile/arrow-down-back.png) 98% 15px no-repeat;
    background-size: 15px;
    outline: none;
    }
    &:last-child{
      margin-right: 0px!important;
      // background: #FFF!important;
      // color:#333333;
    }
  }
  }

}
.input {
        width: 100%;
        height: 3.4rem;
        line-height: 3.4rem;
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
        width: 100%;
        background-color: transparent !important;
        background: transparent !important;
        font-size: 1.2rem;
        color: rgb(149,126,82);
        // padding-left: 20px;
        height: 3.4rem;
        line-height: 3.4rem;
        box-sizing: border-box;
        text-align: center;
        border-radius: 0;
        cursor: pointer;
        font-weight: bold;
        // margin-top: 6px;
        font-family: 'SourceHanSerifCN-Bold';
      }
      input::-webkit-input-placeholder {
        color: rgb(149,126,82);
        font-size: 1.2rem;
        background-color: transparent !important;
      }
      /* Mozilla Firefox 4 to 18 */
      input:-moz-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 1.2rem;
        background-color: transparent !important;
      }
      /* Mozilla Firefox 19+ */
      input::-moz-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 1.2rem;
        background-color: transparent !important;
      }
      /* Internet Explorer 10+ */
      input:-ms-input-placeholder {
        color: rgb(149,126,82);
        font-size: 1.2rem;
        background-color: transparent !important;
      }
      input::-webkit-input-placeholder, textarea::-webkit-input-placeholder { color: rgb(149,126,82); } input:-moz-placeholder, textarea:-moz-placeholder { color: rgb(149,126,82); } input::-moz-placeholder, textarea::-moz-placeholder { color: rgb(149,126,82); } input:-ms-input-placeholder, textarea:-ms-input-placeholder { color: #8a867e; }
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
        width: 100%;
        // border: 1px solid #666666;
        overflow: hidden;
        position: absolute;
        z-index: 1000;
        background-color: #fff;
        cursor: pointer;
        top: 4rem;
        box-sizing: border-box;
        left: 50%;
        transform: translateX(-50%);
        box-shadow: 0 0 5px #eeedec;
      }
      .list > ul > li {
        width: 100%;
        height: 3.5rem;
        cursor: pointer;
        line-height: 3.5rem;
        padding-left: 0;
        border-bottom: 1px solid #ebebeb;
        background: #ffffff;
        text-align: center;
        &:last-child {
          border-bottom: none;
        }
        a {
          color: #999999;
          font-size: 1.2rem;
          text-decoration: none;
        }
      }
      .list > ul > li:hover {
        // background-color: rgba(181, 158, 114, 1);
        a {
          color: rgba(181, 158, 114, 1);
        }
      }
#style-4::-webkit-scrollbar-track {
  // box-shadow:inset 0 0 3px rgba(212,121,77,1);
  background-color: transparent;
}
#style-4::-webkit-scrollbar {
  width: 5px;
  background-color: transparent;
}
#style-4::-webkit-scrollbar-thumb {
  background-color: rgba(0, 0, 0, 0.3);
  // border: 2px solid rgba(0, 0, 0, 0.3);
}
.sortBox{
  position: relative;
  z-index: 1;
  .sortTitle{
    width: 100%;
    height: 3.4rem;
    font-size: 1.2rem;
    text-align: center;
    // text-indent: 33%;
    color: rgb(149,126,82) !important;
    font-weight: 700;
    font-family: 'SourceHanSerifCN-Bold';
    // span{
    //   display: inline-block;
    //   width: 20px;
    //   height: 20px;
    //   background: url(/images/mobile/arrow-down-back.png) center center no-repeat;
    //   background-size: 75%;
    //   margin-left: 24%;
    //   transition: all 0.3s;
    //   vertical-align: middle;
    //   &.rotate{
    //     transform: rotate(180deg);
    //   }
    // }
  }
  .sortList{
    width:100%;
    // border:1px solid #ccc;
    position: absolute;
    left:0;
    top:4rem;
    box-sizing:border-box;
    background: #fff;
    border-radius:3px;
    box-shadow: 0 0 5px #eeedec;
    li{
      text-align: center;
      border-bottom: 1px solid #ebebeb;
      font-size: 1.2rem;
      &:last-of-type{
        border-bottom: none;
      }
    }
  }
}
.ENG{
  .selectBar ul > li {
    b{
      font-family: 'Domine-Bold';
      font-size: 1rem;
    }
    .input{
      input{
        font-family: 'Domine-Bold';
      font-size: 1rem !important;
      color: rgb(149,126,82) !important;
      }
      input::-webkit-input-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 1rem !important;
        background-color: transparent !important;
      }
      /* Mozilla Firefox 4 to 18 */
      input:-moz-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 1rem !important;
        background-color: transparent !important;
      }
      /* Mozilla Firefox 19+ */
      input::-moz-placeholder {
        color: rgb(149,126,82);
        opacity: 1;
        font-size: 1rem !important;
        background-color: transparent !important;
      }
      /* Internet Explorer 10+ */
      input:-ms-input-placeholder {
        color: rgb(149,126,82);
        font-size: 1rem !important;
        opacity: 1;
        background-color: transparent !important;
      }
    }
    .list > ul > li a{
      font-family: 'Domine-Bold';
      font-size: 1rem !important;
      color: rgb(149,126,82) !important;
    }
  }
  .sortBox .sortTitle{
    font-size: 1rem !important;
    font-family: 'Domine-Bold' !important;
  }
  .sortBox .sortList li{
    font-size: 1rem !important;
  }
}
</style>
