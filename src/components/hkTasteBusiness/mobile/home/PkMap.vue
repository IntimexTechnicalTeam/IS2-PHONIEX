<template>
  <div class="map-main">
    <div v-html="mapContent.Body"></div>
  </div>
</template>
<script lang="ts" scoped>
import { Component, Prop, Vue } from 'vue-property-decorator';
@Component
export default class PkMap extends Vue {
  mapContent:string='';
  getmap () {
    this.$Api.cms.getContentByDevice({ Key: 'map', IsMobile: true }).then(result => {
      this.mapContent = result.CMS;
    });
  }

  mounted() {
    this.getmap();
  }
}
</script>

<style scoped lang="less">
/deep/ iframe{
  border: none;
}
.map-main {
  width: 100%;
  margin: 0 auto;
  // display: flex;
  // margin-bottom: 5px;
}
.map-main img {
  width: 100%;
}
</style>
