<template>
  <div class="news" :class="{'ENG' : $Storage.get('locale') === 'E'}">
    <div class="newsW">
      <div class="line"></div>
      <div class="back" v-if="$Storage.get('locale') === 'C'">
        <!-- <h2>{{pencaiTitle}}</h2> -->
        <img src="/images/mobile/M-indexC_03.png" alt="">
      </div>
      <div class="backEng" v-if="$Storage.get('locale') === 'E'">
        <img src="/images/mobile/Mindex_03.png" alt="">
      </div>
    </div>
    <div class="Background">
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
        item.CreateDate = item.CreateDate.substring(0, item.CreateDate.indexOf(' ')
        );
      });
      this.lastestContents = result.Data;
      this.pencaiTitle = result.Data[0].Category.Name;
      for (var i = 0; i < result.Data.length; i++) {
        this.navId = result.Data[i].Id;
      }
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
  margin: 0 auto;
  padding-top: 5.5rem;
  padding-bottom: 3rem;
  overflow: hidden;
  .newsW{
    position: relative;
    .line{
      width: 100%;
      height: 16px;
      background: url(/images/pc/index_27.png) center center;
      background-size: contain;

    }
  .back{
      position: absolute;
      left: 50%;
      transform: translate(-50%,-50%);
      top: 50%;
      // &::after{
      //   content: '';
      //   width: 220px;
      //   height: 70px;
      //   background: url(/images/pc/index_24.png) no-repeat center center;
      //   position: absolute;
      //   background-size: contain;
      //   left: 50%;
      //   transform: translateX(-50%);
      //   top: -15px;
      // }
      img{
        display: block;
        height: 5rem;
      }
    }
  }
  .backEng{
    position: absolute;
    left: 50%;
    transform: translate(-50%,-50%);
    top: 50%;
    img{
      display: block;
      height: 5rem;
    }
  }
  .Background{
    width: 94%;
    margin: 0 auto;
    // min-height: 970px;
    position: relative;
    margin-top: 4rem;
  }
  .tab{
    margin: 0 auto;
    margin-top: 60px;
    width: 100%;
    padding: 1rem;
    box-sizing: border-box;
    border-left: 1px solid #cec0a6;
    border-right: 1px solid #cec0a6;
    position: relative;
    z-index: 100;
    background-color: #fff;
    &::before{
      content: '';
      width: 100%;
      height: 25px;
      background: url('/images/pc/backline_05.png') no-repeat left bottom;
      background-size: contain;
      position: absolute;
      top: 0;
      left: 0;
      transform: rotate(180deg);
    }
    &::after{
      content: '';
      width: 100%;
      height: 25px;
      background: url('/images/pc/backline_05.png') no-repeat right bottom;
      background-size: contain;
      position: absolute;
      bottom: 0;
      right: 0;
    }
    .tab_top{
      margin-bottom: 2rem;
      .tab_top_img{
        margin-bottom: 2rem;
        img{
          width: 100%;
          display: block;
        }
      }
      .tab_top_center{
        margin-bottom: 2rem;
        min-height: 10rem;
        /deep/ p{
          text-align: center;
          font-size: 1.2rem;
        }
      }
    }
    .tab_footer{
      display: flex;
      justify-content: center;
      margin-bottom: 1rem;
      .tab_btn{
        width: 33.33%;
        height: 5rem;
        border: 1px solid #b59e72;
        box-sizing: border-box;
        border-radius: 3px;
        text-align: center;
        margin: 0 5px;
        padding: 8px 0;
        cursor: pointer;
        span{
          font-size: 1.2rem;
          color: #b59e72;
          width: 100%;
          display: block;
          &:first-child{
            overflow: hidden;
            display: -webkit-box;
            -webkit-line-clamp: 1;
            -webkit-box-orient: vertical;
            word-break: break-word;
          }
        }
        &:first-child{
          margin-left: 0;
        }
        &:last-child{
          margin-right: 0;
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
  font-size: 26px;
  color: #fff;
  text-align: center;
  font-family: 'SourceHanSerifCN-Bold';
  position: relative;
  z-index: 10;
  letter-spacing: 16px;
  padding-left: 20px;
}
.news li {
  margin-bottom: 4rem;
}
.news li a {
  width: 100%;
  height: 19.5rem;
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
  font-size: 1.5rem;
  color: #7e7e7e;
  text-align: left;
  height: 3rem;
  line-height: 3rem;
  margin-top: 1rem;
  border-bottom: 1px solid #bdbdbd;

}
.news li .news-title {
  width: 100%;
  font-size: 1.5rem;
  color: #424242;
  text-align: left;
  height: 4rem;
  line-height: 2rem;
  margin-top: 1rem;
  display:flex;
  line-clamp: 2;
  -webkit-box-orient: vertical;
  word-wrap: break-word;
}
.news .more {
  display: block;
  width: 94%;
  margin: 0 auto;
  overflow: hidden;
}
.news .more a {
  color: #7e7e7e;
  font-size: 1.5rem;
  float: right;
}
.ENG.news {
  .tab .tab_footer .tab_btn{
    height: 4rem;
    margin: 0 3px;
    span:first-child{
      font-size: 1.2rem;
    }
    span:last-child{
      font-size: 1rem;
    }
  }
  .tab .tab_top .tab_box .tab_top_center /deep/ p{
      text-align: left;
  }
}
</style>
