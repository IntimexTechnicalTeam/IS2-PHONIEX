<template>
    <div class="fav">
          <div class="headerFav"><a href="javascript:;" @click="tofav" ><img  src="/images/mobile/Mobile-index_02.png"></a></div>
    </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import Cookie from 'js-cookie';
@Component
export default class InsLangSwitch extends Vue {
  tofav () {
    window.location.href = '/account/myFavorite';
  }
}
</script>
<style scoped lang="less">
  .headerFav{
    // padding-left:1rem;
    // padding-right: 1rem;
    float: left;
    width: 3rem;
    height: 3rem;
    box-shadow: 0 0 5px #e9e7e4;
    background-color: #fff;
    border-bottom-left-radius: 20px;
    border-bottom-right-radius: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    // margin-right: 0.3rem;
    img{
      width:1.6rem;
      margin: 0 auto;
      display: block;
    }
  }
</style>
