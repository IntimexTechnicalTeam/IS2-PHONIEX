<template>
  <div id="container" class="PcContact" :class="{'ENG' : $Storage.get('locale') === 'E'}">
  <!-- 联络我们页面 -->
    <div class="Cmsbg" v-if="content.Key === 'ContactUs'">
      <transition name="slide">
        <div key="1" v-if="!waiting">
           <div class="DetailTitle" v-if="ImgList">
            <img :src="ImgList" v-if="ImgList">

          </div>
          <div class="DetailTitle" v-else>
            <div class="TitleBg">
              <div class="innerBoxText">{{CateName}}</div>
            </div>
          </div>
      </div>
      </transition>
      <transition name="slide">
        <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
      </transition>
      <div class="CmsContent">
        <!-- <div class="MapInfo">
          <p class="OurStores">{{$t('Cms.OurStores')}}</p>
          <p class="BusinessHours">{{$t('Cms.BusinessHours')}}: 07:30 - 19:00</p>
          <p v-html="MapInfo" ></p>
        </div> -->
          <div class="CmsMap">
            <p v-html="content.Body" class="cmsbody"></p>
            <!-- <p class="addressIcon"><i></i>{{$t('home.Address')}}：</p>
            <div class="addressBox">
            <div class="perList" v-for="(val,index) in ShopList" :key="index" v-on:click="showContent(val.Id,index)" :class="{'activeColor':cindex==index}">
                <div class="icon"><i></i></div>
                <div class="content">
                  <p>{{val.Title}}</p>
                  <p>{{val.DescOne}}</p>
                  <p>{{val.DescTwo}}</p>
                </div>
              </div>
            </div> -->
          </div>
          <div class="FormMain">
            <!-- <p class="FormTitle">{{FormTitle}}</p> -->
            <div v-html="htmlString" class="to_vertical" id="content"></div>
            <div id="preview" style="display:none;"></div>
          </div>
         <div class="clear"></div>
      </div>
      <!-- <div class="borderline"></div> -->

        <div class="mapbox" v-if="NewcmsId == '20436'">
          <div v-html="mapContent.Body"></div>
        </div>
    </div>
    <!-- 其他页面 -->
    <div class="CmsNormal" v-else>
      <transition name="slide">
        <div key="1" v-if="!waiting">
          <div class="DetailTitle" v-if="OtherPageImg">
            <img :src="OtherPageImg" v-if="OtherPageImg">
          </div>
          <div class="DetailTitle" v-else>
            <div class="TitleBg">
              <div class="innerBoxText">{{TitleName}}</div>
            </div>
          </div>
        </div>
      </transition>
      <transition name="slide">
        <div class="faker" key="2" v-if="waiting" v-loading="true"></div>
      </transition>
      <div class="CmsContent">
        <!-- <p class="TitleName">{{TitleName}}</p> -->
        <p v-html="content.Body"></p>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { FrontE } from '@/sdk/common/SysConst';
import Cookie from 'js-cookie';
@Component({
  components: {
    PkcmsBanner: () => import('@/components/hkTasteBusiness/pc/cms/PkcmsBanner.vue')
  }
})
export default class InsCmsContent extends Vue {
  NewsNav: string = 'NewsNav';
  CateName: string = '';
  CateDesc: string = '';
  content: any[] = [];
  private ImgList: string[] = [];
  private ispic:boolean=false;
  IsMobile:boolean=false;
  MapInfo:string='';
  ShopList:any[]=[];
  FormContent:any='';
  IsPay:boolean= false;
  IsLogin:boolean=false;
  cindex:number=0;
  private htmlString: string = '';
  Signer: any = null;
  FormTitle:string='';
  NewcateId:string='';
  NewcmsId: string='';
  private waiting: boolean = true;
  OtherPageImg:string='';
  TitleName:string='';
  mapContent:string='';

  getForm () {
    this.$Api.regAndPay.getHtml('ContactUs', this.lang, false).then(result => {
      this.htmlString = result.HtmlString;
      this.FormTitle = result.Title;
      this.$nextTick(() => {
        if (document.querySelectorAll('#Sign').length > 0) {
          this.Signer = new intimex.CanvasSigner('#NewSignCanvas', '#Signature', {
            color: '#58B63A',
            width: 5
          });
          this.Signer.initCanvas();
          window['Signer'] = this.Signer;
        }
      });
    });
  }
  get id () {
    return this.$route.params.id ? this.$route.params.id : '';
  }
  get currentlang () {
    return this.$Storage.get('locale');
  }
  getIndexshop () {
    var _this = this;
    this.$Api.cms.getContentsByCatId(40108, 1, 12).then(result => {
      this.ShopList = result.Data;
      result.Data.forEach(function (item) {
        var colon = item.Desc.indexOf('*');
        var a = item.Desc.substring(0, item.Desc.indexOf('*'));
        var b = item.Desc.substr(
          item.Desc.indexOf('*') + 1,
          item.Desc.length
        );
        _this.$set(item, 'DescOne', a);
        _this.$set(item, 'DescTwo', b);
      });
    });
  }
  showContent (val, index) {
    this.$Api.cms.getContentByDevice({ ContentId: val, IsMobile: false }).then(result => {
      this.MapInfo = result.CMS.Body;
      this.cindex = index;
    });
  }
  get lang () {
    return this.$Storage.get('locale');
  }
  get queryLang () {
    return this.$route.query.Lang || '';
  }
  Regnay () {
    window['jsData'] = {
      HasPreview: true,
      UploadButtonText: this.$t('RegNPay.UploadButtonText'),
      UploadingText: this.$t('RegNPay.UploadingText'),
      UploadSuccessfulText: this.$t('RegNPay.UploadSuccessfulText'),
      UploadFailText: this.$t('RegNPay.UploadFailText'),
      NoFileText: this.$t('RegNPay.NoFileText'),
      UploadLengthText: this.$t('RegNPay.UploadLengthText'),
      UploadSizeText: this.$t('RegNPay.UploadSizeText'),
      BackText: this.$t('RegNPay.BackText'),
      ConfirmText: this.$t('RegNPay.ConfirmText'),
      PleaseSelect: this.$t('RegNPay.PleaseSelect'),
      PreviewTitleText: this.$t('RegNPay.PreviewTitleText'),
      RequiredText: this.$t('RegNPay.RequiredText'),
      FormatErrorText: this.$t('RegNPay.FormatErrorText'),
      Version: '2.0',
      HasRNPConfirm: false
    };
    this.$LoadScript('/static/js/CanvasSigner.js');
    this.$LoadScript('/static/js/ajaxFileUpload.js');

    document.dispatchEvent(new Event('rnpFinshed'));

    // RNP Form后台预览跳转语言判断
    if (this.queryLang) {
        this.$Api.member.setUILanguage(this.queryLang).then((result) => {
        this.$i18n.locale = this.queryLang as string;
        localStorage.setItem('locale', this.queryLang as string);
        this.getForm();
      }).catch((error) => {
        console.log(error);
      });
    } else {
      this.getForm();
    }
  }
  created () {
    this.getContent();
    this.getIndexshop();
    this.showContent(20288, 0);
    // this.Regnay();
    this.getmap();
  }
  getContent () {
    this.$Api.cms.getContentByDevice({ Key: this.id, ContentId: this.id, IsMobile: false }).then(result => {
    this.content = result.CMS;
    this.TitleName = result.CMS.Title;
    this.OtherPageImg = result.CMS.Cover;
    this.NewcateId = result.CMS.CatId;
    this.getCategoryByDevice(result.CMS.CatId);
    this.CateDesc = result.CMS.Desc;
    this.NewcmsId = result.CMS.Id;
    this.waiting = false;
    if (result.CMS.Title) document.title = result.CMS.Title;
    console.log(result.CMS, '查查');

    if (result.CMS.Key === 'ContactUs') {
      this.Regnay();
    }
  });
}
getmap () {
    this.$Api.cms.getContentByDevice({ Key: 'map', IsMobile: false }).then(result => {
      this.mapContent = result.CMS;
    });
  }
  // 根据设备类型获取CMSCategory信息
  getCategoryByDevice (cateId) {
      this.$Api.cms.getCategoryByDevice({ CatId: cateId, IsMobile: false }).then(async (result) => {
      this.ImgList = result.ImagePath;
      this.MapInfo = result.Content;
      this.CateName = result.Name;
      this.waiting = false;
    }).catch((error) => {
      console.log(error, 'error');
      this.$message({
        message: error,
        type: 'error'
      });
    });
  }
  @Watch('$route', { deep: true })
  onIdChange () {
    this.getContent();
  }
  mounted () {
    window['regAndPay'] = this.$Api.regAndPay;
    window['router'] = this.$router;
    // window['getPanel'] = this.$Api.getPanel;
    window['Elalert'] = this.$alert;
  }
}
</script>
<style lang="less">
.PcContact .activeColor .content p:nth-child(1){
    color: #333!important;
    text-decoration: underline;
    font-weight: 700;
}
.PcContact .aboutBg{
    width: 80%;
    background-size: 100%;
    min-height: 65rem;
    padding: 10%;
    word-break: break-all;
}
.PcContact .abooutImg{
    width: 70%;
    margin: 0 auto;
    margin-bottom: 1rem;
    img{
      width: 100%;
    }
}
.PcContact .contactBox{
  width: 100%;
  float: left;
  p{
    padding-top: 1.5rem;
    padding-bottom: 1.5rem;
    font-size: 1.4rem;
    display: flex;
    align-items: center;
    border-top: 1px solid #000;
    &:last-child{
      border-bottom: 1px solid #000;
    }
    .icon1{
      background: url('/images/mobile/Mobile-Contact-02.png') no-repeat center center;
      background-size: 100%;
      width: 2rem;
      height: 2rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
    .icon2{
      background: url('/images/mobile/Mobile-Contact-03.png') no-repeat center center;
      background-size: 100%;
      width: 2rem;
      height: 2rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
    .icon3{
      background: url('/images/mobile/Mobile-Contact-04.png') no-repeat center center;
      background-size: 100%;
      width: 2rem;
      height: 2rem;
      display: inline-block;
      vertical-align: middle;
      margin-right:.5rem;
    }
  }
}
.PcContact  .MapInfo{
  width:45%;
  float:left;
  padding-top: 5rem;
  .OurStores{
    font-size: 40px;
    font-weight: 700;
    color:#333333;
  }
  .BusinessHours{
    font-size: 24px;
    color:#333333;
    margin-bottom: 30px;
  }
  iframe{
    width:100%;
    height: 450px;
  }
  img{
    width:100%;
  }
}
.PcContact .FormMain{
  width:50%;
  margin:0 auto;
  // padding-bottom: 3rem;
  position: relative;
  // padding-top: 3rem;
  float: right;
  padding-right: 75px;
  box-sizing: border-box;
  .FormTitle{
    font-size: 40px;
    margin-top: 20px;
    margin-bottom: 20px;
    color:#333333;
  }
  #preview{
    width: 100%;
    // float:right;
    .title{
      font-size: 24px;
      color:#b59e72;
      margin-bottom: 10px;
    }
    .question{
      font-size: 18px;
      margin-bottom: 5px;
      color: #333;
    }
    .anwer{
      margin-bottom: 20px;
      word-break: break-word;
      p{
        word-break: break-word;
        font-size: 16px;
        color: #5e5e5e;
      }
    }
    .back{
      background: #ccc;
      color:#FFF;
      padding:10px 20px 10px 20px;
      border:none;
      margin-right: 20px;
      margin-top: 30px;
    }
    .confirm{
      background: #b59e72;
      color:#FFF;
      padding:10px 20px 10px 20px;
      border:none;
      margin-top: 30px;
      margin-bottom: 30px;
      margin-right: 30px;
    }
  }
  .to_vertical{
    width: 100%;
    display: inline-block;
  }
  .FormImg{
    width: 20%;
    float: left;
    img{
      width: 40%;
    }
  }
  .btn-default{
      width: 100%;
      // float: right;
      background: #b59e72;
      height: 43px;
      line-height: 43px;
      color:#fff;
      background-size: 100%;
      border:none;
      // margin-top: 20px;
      font-size: 22px;
      // margin-bottom: 40px;
      border-radius: 3px;
      font-family: 'SourceHanSerifCN-Bold';
  }
  #Anwers{
    position: relative;
  .form-group{
    width: 100%;
    display: block;
    margin-bottom: 25px;
    // &:nth-child(3){
    //   position: absolute;
    //   width: 100%;
    //   right: 0px;
    //   top:0px;
    // }
    .fieldset{
      border:none;
      padding: 0px;
      margin: 0;
    }
    h4{
      background: #fff;
      background-size: 100% 100%;
      display: inline-block;
      // height: 30px;
      // width: 40%;
      text-align: left;
      // line-height: 3.5rem;
      font-size: 20px;
      margin-bottom: 10px;
      // border:1px solid #808080;
      // border-radius: 2px;
      color: #808080;
      font-weight: 400;
    }
    input[type="text"],input[type="email"]{
      border:1px solid #b59e72;
      height: 43px;
      line-height: 43px;
      width: 100%;
      box-sizing: border-box;
      border-radius: 3px;

      text-indent: 10px;
      outline: none;
      font-size: 18px;
    }
    textarea{
      border:1px solid #b59e72;
      height: 148px;
      width: 100%;
      box-sizing: border-box;
      border-radius: 3px;
      outline: none;
      font-size: 18px;
      // text-indent: 10px;
      padding: 10px;
    }
    p[name="error"]{
      color:red;
      margin-bottom:.5rem;
    }
  }
 }
}
.PcContact .CmsContent{
    position: relative;
    width: 1200px;
    margin: 0 auto;
    margin-top: 50px;
   .aboutUSImg{
     width:10%;
     float:left;
     box-sizing: border-box;
     display: flex;
     justify-content: flex-end;
     margin-left: 3%;
     padding-top: 10%;
     img{
       width: 100%;
     }
   }
   .aboutUSbg{
     width: 83%;
     float: left;
     background: #FFF;
     border-radius: 10px;
     padding: 20px;
     .innerBox{
       border:1px solid #000;
       border-radius: 10px;
       position: relative;
        min-height: 400px;
        padding: 30px;
        word-break: break-all;
        &::before{
            content: '';
            width: 98%;
            height: 20px;
            background-size: 100%;
            position: absolute;
            top: 10px;
            left: 1%;
        }
        &::after{
            content: '';
            width: 98%;
            height: 20px;
            background-size: 100%;
            position: absolute;
            bottom: 10px;
            left: 1%;
        }
     }
   }
  .CmsMapImg{
    width: 30%;
    position: absolute;
    right: 0px;
    top: 3rem;
    text-align: right;
    img{
      width:50%;
    }
  }
  table{
    width: 100%;
  }
  table.about_table{
        width: 980px;
        margin: 0 auto;
        tr{
          display: block;
          margin-bottom: 40px;
          td{
            padding: 20px 40px;
            display: inline-block;
            width: 50%;
            font-size: 20px;
            line-height: 48px;
            box-sizing: border-box;
            text-align: justify;
            img{
              border-radius: 5px;
              display: block;
              box-sizing: border-box;
              width: auto;
              margin: 0 auto;
            }
          }
          td.about_img{
            position: relative;
            border-left: 1px solid #cec0a6;
            border-right: 1px solid #cec0a6;
            display: inline-table;
            padding: 20px 13px !important;
            width: 450px;
            box-sizing: border-box;
            img{
              width: 420px;
              height: 312px;
              display: block;
              margin: 0 auto;
            }
            &::after{
              content: '';
              width: 450px;
              height: 25px;
              background: url('/images/pc/backline_09.png') no-repeat bottom;
              position: absolute;
              bottom: -1px;
              left: 50%;
              transform: translateX(-50%);
            }
            &::before{
              content: '';
              width: 450px;
              height: 25px;
              background: url('/images/pc/backline_09.png') no-repeat bottom;
              position: absolute;
              top: -1px;
              left: 50%;
              transform: translateX(-50%) rotate(180deg);
            }
          }
          &:first-child{
            td:last-child{
              padding-right: 0;
            }
          }
        }
      }
      .table_payment{
        width: 100%;
        tr{
          td{
            padding: 10px;
            display: table-cell;
            width: 14.28%;
            img{
              border: 1px solid #e6e6e6;
              border-radius: 3px;
              display: block;
              box-sizing: border-box;
              width: 100%;
            }
          }
        }
      }
}
.ENG.PcContact{
  .CmsContent table.about_table tr td{
    font-size: 18px;
    line-height: 36px;
    &:last-child{
      padding-top: 0;
    }
  }
  .CmsMap .cmsbody .ContactUs_table{
    tr{
      >td{
          h2{
            font-size: 30px;
          }
          >p:nth-child(2n){
            color: #b59e72;
            font-size: 18px;
            font-family: 'Domine-Bold';
            margin-bottom: 10px;
          }
          >p:nth-child(2n+1){
            color: #333333;
            font-size: 18px;
            font-family: 'Domine-Regular';
            margin-bottom: 20px;
          }
        }
      }
      .ContactUs_table_td{
        height: auto;
        p{
          font-size: 22px;
          font-family: 'Domine-Bold' !important;
          line-height: 34px;
        }
        .ContactUs_table_back{
          height: auto;
          padding-bottom: 14px;
        }
      }
    }
  .FormMain /deep/ .form-group .btn-default{
    font-family: 'Domine-Bold' !important;
  }
}
iframe{
  border: none;
}
</style>
<style scoped lang="less">
.TitleName{
  text-align: center;
  font-size: 1.6rem;
  font-weight: 700;
  margin-bottom: 2rem;
}
.DetailTitle{
  width: 100%;
  display: block;
  // flex-wrap:wrap;
  // position: relative;
  // align-items: center;
  // justify-content: center;
  img{
    width: 100%;
  }
  .TitleBg{
      width: 254px;
      height: 50px;
      background: url(/images/pc/index_66.png) no-repeat center center;
      margin: 0 auto;
      text-align: center;
      margin: 50px auto;
      .innerBoxText{
        font-size: 20px;
        font-family: 'SourceHanSerifCN-Medium';
        color: #b59e72;
        line-height: 50px;
      }
    }
}

.CmsNormal{
  width: 100%;
  display: block;
  background: #FFF;
  padding-bottom: 60px;
}
.TitleBg{
    // width: 500px;
    // border: 1px solid #ffffff;
    // height: 70px;
    // line-height: 70px;
    // margin: 0 auto;
    // padding: 10px;
    // margin-bottom: 20px;
    // top: 50%;
    // position: absolute;
    // transform: translateY(-50%);
    margin: 50px auto;
    .innerBoxText{
      // background:#ffffff;
      color: #b59e72;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 30px;
      font-weight: 700;
      // font-family: 'Arial';
    }
  }
.Cmsbg{
    width:100%;
    // background: url('/images/pc/pccontact_01.jpg') no-repeat center center;
    // background-size:100% 100%;
    display: block;
    box-sizing: border-box;
    display: inline-block;
    .borderline{
      height:1px;
      width: 100%;
      display: inline-block;
      background: #000;
   }
   .mapbox{
    margin-top: 50px;
   }
}
.Banner {
  width: 100%;
  height: 20rem;
  display:flex;
  align-items: center;
  padding-left:2rem;
  .innerBox{
      width: 1200px;
      margin: 0 auto;
  }
}
.Banner img {
  width: 100%;
  height: 20rem;
}
.CmsMap {
    width: 50%;
    float: left;
    // margin-left: 5%;
    display: flex;
    flex-wrap: wrap;
    // padding-top: 5rem;
    padding-left: 75px;
    box-sizing: border-box;
    .cmsbody{
      width:100%;
      /deep/ .ContactUs_table{
    width: 100%;
    tr{
      td{
        h2{
          font-family: 'SourceHanSerifCN-Bold';
          font-size: 40px;
          margin-bottom: 34px;
          color: #b59e72;
        }
        // p{
        //   font-size: 20px;
        //   color: #4e4e4e;
        //   // margin-bottom: 32px;
        //   display: flex;
        //   align-items: center;
        //   img{
        //     margin-right: 10px;
        //   }
        // }
        >p:nth-child(2n){
            color: #b59e72;
            font-size: 20px;
            margin-bottom: 10px;
          }
          >p:nth-child(2n+1){
            color: #333333;
            font-size: 20px;
            margin-bottom: 20px;
          }
      }
      .ContactUs_table_td{
        width: 450px;
        height: 180px;
        position: relative;
        border-left: 1px solid #cec0a6;
        border-right: 1px solid #cec0a6;
        display: block;
        // margin-left: auto;
        box-sizing: border-box;
        // margin-top: 34px;
        &::after{
              content: '';
              width: 450px;
              height: 25px;
              background: url('/images/pc/backline_13.png') no-repeat bottom;
              background-size: cover;
              position: absolute;
              bottom: -1px;
              left: 50%;
              transform: translateX(-50%);
            }
            &::before{
              content: '';
              width: 450px;
              height: 25px;
              background: url('/images/pc/backline_13.png') no-repeat bottom;
              background-size: cover;
              position: absolute;
              top: -1px;
              left: 50%;
              transform: translateX(-50%) rotate(180deg);
            }
        p{
          color: #fff;
          font-size: 24px;
          font-family: 'SourceHanSerifCN-Bold';
          line-height: 40px;
          text-align: center;
          justify-content: center;
        }
        .ContactUs_table_back{
          width: 420px;
          height: 150px;
          text-align: center;
          background: url('/images/pc/index_115.png') center center;
          display: block;
          margin: 14px;
          padding-top: 14px;
          box-sizing: border-box;
        }
      }
    }
  }
    }
    .addressBox{
      width: 100%;
      float: left;
      display: flex;
      flex-wrap: wrap;
    }
}

.CmsMap
{
  position: relative;
  .addressIcon{
    width: 100%;
    font-size: 1.4rem;
    margin-bottom: 2rem;
    padding-top: 2rem;
    align-items: center;
    display: flex;
    i{
      background: url('/images/mobile/Mobile-Contact-05.png') no-repeat center center;
      background-size: 100% 100%;
      width:2rem;
      height:2rem;
      display: inline-block;
      margin-right:.5rem;
    }
  }
  .perList{
    margin-bottom: 3rem;
    width: 48%;
    margin-right:4%;
    &:nth-child(2n){
      margin-right:0%!important;
    }
    p{
      font-size:1.4rem;
      cursor: pointer;
      &:nth-child(1):hover{
        color:#262626 ;
      }
    }
  }
}
.clear {
  clear: both;
}

</style>
