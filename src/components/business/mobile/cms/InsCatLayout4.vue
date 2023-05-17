<template>
    <div class="cms-list">
        <ul>
            <li v-for="(cms,index) in cmsData" :key="index">
                <div class="cmsData_url" v-if="cms.Url">
                    <a @click="toUrl(cms)" >
                        <div class="cover">
                            <img :src="cms.Cover" alt=""/>
                        </div>
                        <div class="introduce">
                            <p class="title">{{cms.Title}}</p>
                            <p class="desc">{{cms.Desc}}</p>
                            <p class="viewMore"><span>{{$t('Message.ViewDetail')}}</span></p>
                        </div>
                    </a>
                </div>
                <div v-else>
                    <router-link :to="'/cms/content/'+cms.Id">
                        <div class="cover">
                            <img :src="cms.Cover" alt=""/>
                        </div>
                        <div class="introduce">
                            <p class="title">{{cms.Title}}</p>
                            <!-- <p class="createDate">{{cms.CreateDate}}</p> -->
                            <p class="desc">{{cms.Desc}}</p>
                            <p class="viewMore"><span>{{$t('Message.ViewDetail')}}</span></p>
                        </div>
                    </router-link>
                </div>
            </li>
        </ul>

        <div class="loading_box" ref="load" @touchstart="loading" v-if="pager.totalRecord > pager.pageSize">
            <span class="loading_title">{{tips?$t('Action.LoadMore'):$t('home.Thatsall')}}</span>
        </div>
        <div class="loading_box" v-else>
            <span class="loading_title">{{$t('home.Thatsall')}}</span>
        </div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator';
import { Loading } from 'element-ui';
@Component
export default class InsCatLayout1 extends Vue {
    @Prop({ default: () => [] }) private cmsData!: object[]; // cms内容列表数据
    @Prop({ default: () => {} }) private pager!: any; // 分頁器數據

    private tips:boolean = true;
    private LoadingInstance!: any;

    toUrl (n) {
    //   console.log(n, '检查URL');
      if (!n.IsOpenWindows && n.Url) {
        window.location.href = n.Url;
      } else if (n.IsOpenWindows && n.Url) {
        window.open(n.Url);
      }
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
        if (this.pager.totalRecord !== this.cmsData.length) {
          this.pager.currentPage++;
        } else {
            this.tips = false;
        }
    }
}
</script>

<style scoped lang="less">
.cms-list {
    ul {
        width: 100%;

        >li{
        //  width: 23%;
        width: 100%;
        //  float: left;
        //  margin-right: 2.66%;
        margin-bottom: 2rem;
        //  &:nth-child(4n){
        //    margin-right: 0%!important;
        //  }
        a{
            display:block;
            border:1px solid #eee;
            border-radius: 10px;
            overflow: hidden;
        //  transition: all .3s ease;
        // -o-transition: all .3s ease;
        // -webkit-transition: all .3s ease;
        // -moz-transition: all .3s ease;
        //  &:hover{
        //    border:1px solid #00d0b5;
        //    .introduce{
        //      .title{
        //        color:#00d0b5;
        //      }
        //    }
        //  }
        }
        .introduce{
            width: 90%;
            margin: 0 auto;
            //  padding-bottom: 20px;
            padding-bottom: 1.6rem;
            .title{
            //  font-size:18px;
            font-size: 1.4rem;
            font-weight: bold;
            padding-bottom: 10px;
            border-bottom: 1px solid #eee;
            color:#b59e72;
            margin-bottom: 5px;
            text-overflow: -o-ellipsis-lastline;
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            line-clamp: 2;
            -webkit-box-orient: vertical;
            max-height: 44px;
            margin-top: 20px;
            }
            .createDate{
            //  font-size: 16px;
            font-size: 1.2rem;
            color:#000000;
            padding-bottom: 10px;
            }
            .desc{
            //  font-size: 14px;
            font-size: 1.3rem;
            color:#000000;
            //  margin-bottom: 40px;
            margin-bottom: 2rem;
            text-overflow: -o-ellipsis-lastline;
            overflow: hidden;
            text-overflow: ellipsis;
            display: -webkit-box;
            -webkit-line-clamp: 2;
            line-clamp: 2;
            -webkit-box-orient: vertical;
            // max-height: 36px;
            }
            .viewMore{
            text-align: center;
            display: inline-block;
            width: 100%;
            span{
                //padding: 5px 20px;
                padding: 0.4rem 2.8rem;
                font-size: 1.32rem;
                font-weight: bold;
                background: @base_color;
                display: inline-block;
                color:#fff;
            }
            }
        }
        .cover{
            width: 100%;
            img{
                width: 100%;
                height: 34rem;
                object-fit: contain;
                display: block;
            }
        }
        }
    }

    .loading_box{
        position: relative;
        text-align: center;
        margin-bottom: 3rem;
        margin-top: 3rem;

        &:before{
            background:#ddd;
            content:"";
            display: block;
            height: 1px;
            position: absolute;
            top: 50%;
            width: 100%;
        }

        .loading_title{
        background: #fff;
        padding: 0 3rem;
        position: relative;
        z-index: 1;
        font-size: 1.5rem;
        color: #787878;
        }
    }
}
</style>
