<template>
  <div class="home" v-cloak>
    <HomeBanner :page="'Home'" class="banner" />
    <PkNews/>
    <HkPromotion />
    <Hkpayment />
    <HkBranch />
    <HkLiveBox />
    <PkMap/>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component({
  components: {
    HomeBanner: () => import('@/components/base/mobile/InsBanner.vue'),
    HkPromotion: () => import('@/components/hkTasteBusiness/mobile/home/HkPromotion.vue'),
    HkBranch: () => import('@/components/hkTasteBusiness/mobile/home/HkBranch.vue'),
    HkLiveBox: () => import('@/components/hkTasteBusiness/mobile/home/HkLiveBox.vue'),
    PkNews: () => import(/* webpackChunkName: "home" */ '@/components/hkTasteBusiness/mobile/home/PkNews.vue'),
    Hkpayment: () => import(/* webpackChunkName: "home" */ '@/components/hkTasteBusiness/mobile/home/Hkpayment.vue'),
    PkMap: () => import(/* webpackChunkName: "home" */ '@/components/hkTasteBusiness/mobile/home/PkMap.vue')
  }
})
export default class InsHome extends Vue {
  swiperOptionhome: object = {
    effect: 'fade',
    autoplay: {
      disableOnInteraction: false
    },
    pagination: {
      el: '.swiper-pagination.swiper-paginationhome',
      clickable: true
    }
  };
}
</script>

<style lang="less" scoped>
.banner {
  // min-height: 100vh;
  background-color: white;
}
</style>
