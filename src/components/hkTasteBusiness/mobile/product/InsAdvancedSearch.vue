<template>
    <div class="InsAdvancedSearch" :class="{'ENG' : $Storage.get('locale') === 'E'}">
      <div class="AdvancedSearchW">
        <p class="resetTitle">{{$t('product.Screening')}}<span class="el-icon-close" @click="closeSub"></span></p>
       <p class="resetAll" @click="resetAll">{{$t('product.Resetall')}}</p>
      </div>
      <div id="style-4" class="scrollbar">
          <ul class="attrSearch" v-if="init">
            <ReSearchItem v-for="(attr, index) in attrList" :key="index" :searchGroup="attr" :defaultSelected="deAttrGIds.indexOf(attr.Id) !== -1 ? selectedAttrs[deAttrGIds.indexOf(attr.Id)].Vals : []"  @changeSelect="changeAttrSelect" />
          </ul>
          <ul class="catSearch" v-if="init">
            <ReSearchItem v-for="(cat, index) in catalogs" :key="index" :searchGroup="cat" :defaultSelected="deCatGIds.indexOf(cat.Id) !== -1 ? selectedCats[deCatGIds.indexOf(cat.Id)].Vals : []" :searchType="2"  @changeSelect="changeCatSelect" />
          </ul>
          <ul class="priceRange" v-if="prices">
            <p class="priceName">
              {{$t('product.PriceScreening')}}
              <i class="el-icon-arrow-up"  @click="isOpen = !isOpen" v-if="!isOpen"></i>
              <i class="el-icon-arrow-down" @click="isOpen = !isOpen" v-else></i>
            </p>
            <div :class="{'open': isOpen}">
              <el-slider
                v-model="Pricevalue"
                range
                :show-tooltip="false"
                @change="getVal"
                :step="10"
                :max="9999">
              </el-slider>
              <p class="priceTitle">
                <span class="T1">HKD {{Pricevalue[0] | PriceFormat}}</span>
                <span class="T2">HKD {{Pricevalue[1] | PriceFormat}}</span>
              </p>
            </div>
        </ul>
      </div>

    </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
export interface attrItem {
  Id: number,
  Vals: number[]
}
@Component({
  components: {
    ReSearchItem: () => import('@/components/hkTasteBusiness/mobile/product/ReSearchItem.vue')
  }
})
export default class InsAdvancedSearch extends Vue {
    @Prop({ default: 0 }) private attrType!: number; // 产品属性数据类型（0 => 所有属性， 1 => 仅库存属性， 2 => 仅非库存属性）
    @Prop({ type: Boolean, default: true }) private prices!: boolean; // 是否開啟價格區間篩選

    attrList: any[] = []; // 产品属性数据
    catalogs: any[] = []; // 产品目录数据
    checkedValue: string[] = []; // 选中的产品属性值
    selectedAttrs: attrItem[] = []; // 选中的产品属性值
    deAttrGIds: number[] = []; // url获取的选中产品属性组id
    selectedCats: attrItem[] = []; // 选中的产品目录数据
    deCatGIds: number[] = []; // url获取的选中产品目录组id
    paramCats: number[] = []; // 选中的产品目录数据传参数组
    init: boolean = false; // 判断是否渲染子组件标识

    Pricevalue:number[]=[0, 9999]; // 價格篩選區間
    isOpen: boolean = false; // 是否展开

    // 获取产品库存属性
    async getAttrList (Type) {
      await this.$Api.prodAttrApi.getAttrList({ type: Type }).then((result) => {
        console.log(result, '获取产品库存属性');
        this.attrList = result;

        // this.setDefaultChecked();
      });
    }

    getVal () {
      this.$emit('advancedChange', this.selectedAttrs, this.paramCats, this.Pricevalue);
    }
    // 关闭搜索列表
    closeSub () {
      this.$emit('closeSub');
    }
    resetAll () {
      this.$emit('resetAll');
    }
    // 获取产品目录列表
    async getCatalogs () {
      await this.$Api.product.getAttrList().then((result) => {
        this.catalogs = result;
        console.log(result, '获取产品目录列表');
      });
    }

    // 设置默认选择
    setDefaultChecked () {
      console.log('设置默认选择');
      if (this.selectedAttrs.length) {
        this.selectedAttrs.forEach(element => {
          if (!element.Vals.length) {
            for (let i = 0; i < this.attrList.length; i++) {
              if (element.Id === this.attrList[i].Id) {
                this.attrList[i].AttrValues.forEach(v => {
                  element.Vals.push(v.Id);
                });
                break;
              }
            }
          }
        });
      }

      if (this.paramCats.length) {
        this.catalogs.forEach((element, i) => {
          if (this.paramCats.indexOf(element.Id) !== -1) {
            this.selectedCats.push({
              Id: element.Id,
              Vals: []
            });
            element.Children.forEach(item => {
              this.selectedCats[this.selectedCats.length - 1].Vals.push(item.Id);
              this.paramCats.push(item.Id);
            });
          } else {
            this.selectedCats.push({
              Id: element.Id,
              Vals: []
            });

            let flag = 0;
            element.Children.forEach(item => {
              if (this.paramCats.indexOf(item.Id) !== -1) {
                flag = 1;
                this.selectedCats[this.selectedCats.length - 1].Vals.push(item.Id);
              }
            });

            if (!flag) {
              this.selectedCats.splice(-1, 1);
            }
          }
        });

        this.deCatGIds = [];
        this.selectedCats.forEach((element, i) => {
          this.deCatGIds.push(element.Id);
        });
      }

      /* Style4高级搜索组件定制需求
        菜单点击产品库存属性选中任意一个，产品目录全选中，
        菜单点击产品目录选中任意一个，产品库存属性全选中
      */
      // if (!this.routerType && this.selectedAttrs.length) {
      //   this.selectedCats = [];

      //   this.catalogs.forEach((element, i) => {
      //     this.deCatGIds.push(element.Id);
      //     this.selectedCats.push({
      //       Id: element.Id,
      //       Vals: []
      //     });

      //     element.Children.forEach(item => {
      //       this.selectedCats[i].Vals.push(item.Id);
      //       this.paramCats.push(item.Id);
      //     });
      //   });
      // } else if (!this.routerType && this.paramCats.length) {
      //   this.selectedAttrs = [];
      //   this.deAttrGIds = [];
      //   this.attrList.forEach((element, i) => {
      //     this.deAttrGIds.push(element.Id);
      //     this.selectedAttrs.push({
      //       Id: element.Id,
      //       Vals: []
      //     });

      //     element.AttrValues.forEach(item => {
      //       this.selectedAttrs[i].Vals.push(item.Id);
      //     });
      //   });
      // }

      this.$nextTick(() => {
        this.init = true;
      });

      // this.$router.push(`${this.$route.path}?attrs=${JSON.stringify(this.selectedAttrs)}&catalogs=${JSON.stringify(this.selectedCats)}&type=${this.routerType}`);
      window.history.replaceState(null, '', this.$route.path + '?attrs=' + JSON.stringify(this.selectedAttrs) + '&catalogs=' + JSON.stringify(this.paramCats) + '&type=' + this.routerType);
      this.$emit('advancedChange', this.selectedAttrs, this.paramCats);
    }

    // 产品属性搜索条件改变
    changeAttrSelect (Id, attr) {
      this.deAttrGIds = [];
      let flag = 0;
      for (let i = 0; i < this.selectedAttrs.length; i++) {
        if (this.selectedAttrs[i].Id === Id) {
          flag = 1;
          if (attr.length) {
            this.selectedAttrs[i].Vals = attr;
            break;
          } else {
            this.selectedAttrs.splice(i, 1);
          }
        }
      }

      if (!flag) {
        this.selectedAttrs.push({
          Id: Id,
          Vals: attr
        });
      }

      // this.$router.push(`${this.$route.path}?attrs=${JSON.stringify(this.selectedAttrs)}&catalogs=${JSON.stringify(this.selectedCats)}&type=1`);
      window.history.replaceState(null, '', this.$route.path + '?attrs=' + JSON.stringify(this.selectedAttrs) + '&catalogs=' + JSON.stringify(this.paramCats) + '&type=1');
      this.$emit('advancedChange', this.selectedAttrs, this.paramCats);
    }

    // 产品目录搜索条件改变
    changeCatSelect (Id, cat) {
      this.deCatGIds = [];
      let flag = 0;
      for (let i = 0; i < this.selectedCats.length; i++) {
        if (this.selectedCats[i].Id === Id) {
          flag = 1;
          if (cat.length) {
            this.selectedCats[i].Vals = cat;
            break;
          } else {
            this.selectedCats.splice(i, 1);
          }
        }
      }

      if (!flag) {
        this.selectedCats.push({
          Id: Id,
          Vals: cat
        });
      }

      this.paramCats = [];
      this.selectedCats.forEach(element => {
        element.Vals.forEach(v => {
          this.paramCats.push(v);
        });
        if (element.Vals.length === 0) {
           this.paramCats.push(element.Id);
        } else {
          element.Vals.forEach(v => {
            this.paramCats.push(v);
          });
        }
      });
      // this.$router.push(`${this.$route.path}?attrs=${JSON.stringify(this.selectedAttrs)}&catalogs=${JSON.stringify(this.selectedCats)}&type=1`);
      window.history.replaceState(null, '', this.$route.path + '?attrs=' + JSON.stringify(this.selectedAttrs) + '&catalogs=' + JSON.stringify(this.paramCats) + '&type=1');
      this.$emit('advancedChange', this.selectedAttrs, this.paramCats);
    }

    //  url 获取默认选中值
    async getDefaultSelected () {
      this.deAttrGIds = [];
      this.deCatGIds = [];
      this.selectedCats = [];
      this.selectedAttrs = JSON.parse(this.$route.query.attrs as string || '[]');
      this.selectedAttrs.forEach(element => {
        this.deAttrGIds.push(element.Id);
      });

      this.paramCats = JSON.parse(this.$route.query.catalogs as string || '[]');

      return Promise.resolve();
    }

    get routerType () {
      return JSON.parse(this.$route.query.type as string || '0');
    }

    mounted () {}

    created () {
      Promise.all([this.getDefaultSelected(), this.getAttrList(this.attrType), this.getCatalogs()]).then((result) => {
        this.setDefaultChecked();
      });
    }

  @Watch('$route', { deep: true })
    onRouteChange (n, o) {
      if (!this.routerType) {
        console.log('route变啦');
        this.init = false;

        this.getDefaultSelected().then(() => {
          this.setDefaultChecked();
        });
      }
    }
}
</script>
<style scoped lang="less">
.InsAdvancedSearch {

  .resetAll{
    width: 92%;
    height: 50px;
    background: url('/images/pc/screening_btn.png') no-repeat center center;
    background-size: contain;
    border-radius: 3px;
    // padding-left: 1rem;
    // padding-top: 30px;
    // padding-bottom: 20px;
    margin: 0 auto;
    margin-bottom: 0;
    color: #FFF;
    font-size: 1.6rem;
    // text-decoration: underline;
    cursor: pointer;
    text-align: center;
    line-height: 50px;
  }
  .resetTitle{
    background: #fff;
    padding-left: 1rem;
    padding-top: 1.5rem;
    padding-bottom: 1.5rem;
    color:#333333;
    font-size: 1.6rem;
    font-family: 'SourceHanSerifCN-Bold';
    span{
      float:right;
      margin-right: 1rem;
      font-size: 2.5rem;
      cursor: pointer;
      color: #b59e72;
      font-weight: bold;
    }
  }
  .priceRange {
    padding: 1rem;
    background-color: #fff;

    .priceName {
      color: #333333;
      font-size: 1.6rem;
      margin-bottom: 1rem;
      position: relative;
      i{
        position: absolute;
        right: 0px;
        top: 0;
        font-size: 26px;
        color: #b59e72;
        font-weight: bold;
      }
    }

    /deep/ .el-slider {
      padding: 0 12px;

      .el-slider__bar {
        background-color: @base_color;
      }

      .el-slider__button {
        border-color: @base_color;
      }
    }

    .priceTitle {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin: 0;

      span {
        font-size: 1.2rem;
        color: #999;
      }
    }
    .open {
      display: none;
      transition: all 3s;
    }
  }
}
.ReSearchItem{
    position: relative;
    background: #FFF;
    padding: 1rem;
    border-bottom: 1px solid #eee;
    // &::before{
    //     content: '';
    //     width: 95%;
    //     height: 10px;
    //     background: url(/images/mobile/productList_icon.png) no-repeat center center;
    //     background-size: contain;
    //     position: absolute;
    //     top: 5px;
    //     left: 2.5%;
    //   }
    //    &::after{
    //     content: '';
    //     width: 95%;
    //     height: 10px;
    //     background: url(/images/mobile/productList_icon.png) no-repeat center center;
    //     background-size: contain;
    //     position: absolute;
    //     bottom: 5px;
    //     left: 2.5%;
    //   }
  }
  .ENG.InsAdvancedSearch{
     .resetTitle span{
      font-family: element-icons!important;
     }
     .ReSearchItem /deep/ p.category{
        font-size: 1.6rem;
        font-family: 'Domine-Bold';
     }
     .ReSearchItem /deep/ ul > li{
      height: auto;
      label{
        height: auto;
        font-size: 1.5rem;
        padding: 1rem;
      }
     }
  }
</style>
