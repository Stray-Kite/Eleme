<template>
  <div>
    <!-- 头部 -->
    <v-header :seller="seller"></v-header>
    <!--&lt;!&ndash; 主体切换 &ndash;&gt;-->
    <div class="tab border-1px">
      <div class="tab-item">
        <router-link to="/goods">
          商品
        </router-link>
      </div>
      <div class="tab-item">
        <router-link to="/ratings">
          评论
        </router-link>
      </div>
      <div class="tab-item">
        <router-link to="/seller">
          商家
        </router-link>
      </div>
    </div>
     <!--头部-->

    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>
<script>
  import header from './components/header/header.vue';
  // import {urlParse} from './common/js/util';
  import data from './common/json/data';
  export default {
    data() {
      return {
        seller: {},
        // id: (() => {
        //   let queryParam = urlParse();
        //   console.log(queryParam);
        //   return queryParam.id;
        // })()
      };
    },
    created() {
      this.seller = data.seller;
    },
    components: {
      'v-header': header
    }
  };
</script>
<style lang="stylus">
  @import "common/stylus/mixin.styl";

  .tab {
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    border-1px(rgba(7, 17, 27, 0.1));
  }
  .tab .tab-item {
    flex: 1;
    text-align: center;
  }
  .tab .tab-item a {
    display: block;
    font-size: 14px;
    color: rgb(77, 85, 93);
  }
  .tab .tab-item .active {
    color: rgb(240, 20, 20);
  }
</style>
