<template>
  <div class="news" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="newsW">
      <div class="line"></div>
      <div class="back" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{pencaiTitle}}</h2> -->
        <img src="/images/pc/M-indexC_03.png" alt="">
      </div>
      <div class="back" v-if="$Storage.get('locale') === 'E'">
        <img src="/images/pc/Mindex_03.png" alt="">
      </div>
    </div>
    <div class="Background">
      <!-- <ul>
        <li v-for="(n,index) in lastestContents" :key="index">
          <router-link :to="'/cms/content/'+n.Id">
            <img :src="n.Cover" class="NewsPart" />
          </router-link>
          <p class="news-date">{{n.CreateDate}}</p>
          <p class="news-title">{{n.Title}}</p>
        </li>
      </ul> -->
      <div class="tab" >
        <div class="tab_top">
          <div class="tab_box" v-for="(n,index) in lastestContents" :key="index" >
            <div v-show="navId===null || navId===n.Id">
              <div class="tab_top_img">
                <img :src="n.Cover" class="NewsPart" alt="">
              </div>
              <div class="tab_top_center" v-html="n.Body"></div>
            </div>
          </div>
        </div>
        <div class="tab_footer">
          <div class="tab_btn" v-for="(a,index2) in lastestContents" :key="index2" @click="navId=a.Id" :class="{isActive:navId==a.Id}">
            <span>{{a.Title}}</span>
            <span>{{a.Desc}}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- <p class="more">
      <a href="#">{{$t('home.More')}}></a>
    </p> -->
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
@Component
export default class PkNews extends Vue {
  lastestContents: any[] = [];
  pencaiTitle: string = '';
  private navId:any = 0;
  getNews () {
    var cond = {
      Page: 1,
      PageSize: 3,
      catId: 40127
    };
    this.$Api.cms.getLastestContents(cond).then(result => {
      result.Data.forEach(function (item) {
        item.CreateDate = item.CreateDate.substring(
          0,
          item.CreateDate.indexOf(' ')
        );
      });
      this.lastestContents = result.Data;
      this.pencaiTitle = result.Data[0].Category.Name;
      for (var i = 0; i < result.Data.length; i++) {
        this.navId = result.Data[i].Id;
      }
      console.log(result, '扫');
      console.log(this.navId, '查ID');
    });
  }
  mounted () {
    this.getNews();
  }
}
</script>

<style scoped lang="less">
.news {
  width: 100%;
  /*height:800px;*/
  // padding-bottom: 80px;
  padding-top: 80px;
  box-sizing: border-box;
  .newsW{
    position: relative;
    .line{
      width: 100%;
      height: 19px;
      background: url(/images/pc/index_27.png) center center;

    }
  .back{
      position: absolute;
      left: 50%;
      transform: translate(-50%,-50%);
      top: 50%;
      img{
        height: 80px;
        display: block;
      }
      // &::after{
      //   content: '';
      //   width: 230px;
      //   height: 80px;
      //   background: url(/images/pc/index_24.png) no-repeat center center;
      //   position: absolute;
      //   left: 50%;
      //   transform: translateX(-50%);
      //   top: -16px;
      // }
    }
  }
  .Background{
    width: 100%;
    min-height: 970px;
    position: relative;
    &::before{
      content: '';
      width: 540px;
      height: 757px;
      background: url('/images/pc/index_29.png') no-repeat left;
      position: absolute;
      top: 0;
      left: 0;
    }
    &::after{
      content: '';
      width: 540px;
      height: 757px;
      background: url('/images/pc/index_36.png') no-repeat right;
      position: absolute;
      bottom: 0;
      right: 0;
    }
  }
  .tab{
    margin: 0 auto;
    margin-top: 60px;
    width: 640px;
    padding: 19px;
    box-sizing: border-box;
    border-left: 1px solid #cec0a6;
    border-right: 1px solid #cec0a6;
    position: relative;
    z-index: 1000;
    background-color: #fff;
    &::before{
      content: '';
      width: 640px;
      height: 25px;
      background: url('/images/pc/backline_05.png') no-repeat left;
      position: absolute;
      top: 0;
      left: 0;
      transform: rotate(180deg);
    }
    &::after{
      content: '';
      width: 640px;
      height: 25px;
      background: url('/images/pc/backline_05.png') no-repeat right;
      position: absolute;
      bottom: 0;
      right: 0;
    }
    .tab_top{
      margin-bottom: 30px;
      .tab_top_img{
        margin-bottom: 30px;
        img{
          width: 100%;
          display: block;
        }
      }
      .tab_top_center{
        margin-bottom: 30px;
        min-height: 170px;
        /deep/ p{
          text-align: center;
        }
      }
    }
    .tab_footer{
      display: flex;
      justify-content: center;
      .tab_btn{
        width: 180px;
        height: 70px;
        border: 1px solid #b59e72;
        box-sizing: border-box;
        border-radius: 3px;
        text-align: center;
        margin: 0 10px;
        padding: 8px 0;
        cursor: pointer;
        span{
          font-size: 18px;
          color: #b59e72;
          width: 100%;
          display: block;
        }
      }
    }
    .isActive{
      display: block !important;
      background-color: #b59e72;
      span{
        color: #fff !important;
      }
    }
    .isActivehide{
      display: none !important;
    }
  }
}

.news h2 {
  font-size: 30px;
  color: #fff;
  text-align: center;
  font-family: 'SourceHanSerifCN-Bold';
  position: relative;
  z-index: 10;
  letter-spacing: 20px;
  padding-left: 20px;

}
.news ul {
  width: 1120px;
  margin: 0 auto;
  overflow: hidden;
}
.news li {
  width: 326px;
  margin-right: 71px;
  float: left;
  box-sizing: border-box;
}
.news li:nth-child(3n) {
  margin-right: 0;
}
.news li a {
  display: block;
  width: 100%;
  height: 210px;
  background-color: #ededed;
  display: flex;
  justify-content: center;
  align-items: center;
}
.news li a img {
  max-width: 100%;
  max-height: 100%;
}
.news li .news-date {
  display: inline-block;
  width: 100%;
  font-size: 16px;
  color: #7e7e7e;
  text-align: left;
  height: 48px;
  line-height: 60px;
  border-bottom: 1px solid #bdbdbd;
}
.news li .news-title {
  width: 100%;
  font-size: 16px;
  color: #424242;
  text-align: left;
  height: 40px;
  line-height: 20px;
  margin-top: 10px;
  display: flex;
  display: -webkit-box;
  display: -moz-box;
  line-clamp: 2;
  -moz-line-clamp: 2;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  word-wrap: break-word;
}
.news .more {
  display: block;
  width: 1120px;
  margin: 0 auto;
  overflow: hidden;
  margin-top: 75px;
}
.news .more a {
  color: #7e7e7e;
  font-size: 16px;
  float: right;
}
.ENG.news{
  .tab .tab_footer .tab_btn{
    padding: 10px 0;
    span{
      margin-bottom: 8px;
    }
  }
  .tab .tab_top .tab_box .tab_top_center /deep/ p{
      text-align: left;
  }
}
</style>
