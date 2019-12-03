<template>
  <div id="home">
    <NavBar class="home-nav">
      <div slot="center">购物街</div>
    </NavBar>

    <TabControl :titles="['流行','新款','精选']" @tabCilck="tabClick" v-show="isTabFixed" class="tab-control" ref="tabControl1" ></TabControl>

    <Scroll class="content" ref="scroll" :probeType="3" :pullUpLoad="true" @scroll="contentScroll" @loadMore="toToadMore">
      <HomeSwiper :banners="banners" @swiperImgLoad="swiperImgLoad"></HomeSwiper>
      <HomeRecommendView :recommends="recommends"></HomeRecommendView>
      <HomeFeaturView></HomeFeaturView>
      <TabControl :titles="['流行','新款','精选']" @tabCilck="tabClick" class="tab-control" ref="tabControl2"></TabControl>
      <GoodsList :goods="goods[currentType].list"></GoodsList>
    </Scroll>
    <BackTop v-show="isShow" @click.native="backTopClick"></BackTop>
  </div>

</template>

<script>
  import HomeSwiper from "./homeChild/HomeSwiper";
  import HomeRecommendView from "./homeChild/HomeRecommendView";
  import HomeFeaturView from "./homeChild/HomeFeaturView";


  import GoodsList from "components/content/goods/GoodsList";
  import BackTop from "components/common/backTop/BackTop";
  import NavBar from "components/common/navbar/NavBar";
  import TabControl from "components/content/tabControl/TabControl";
  import Scroll from "components/common/srcoll/Scroll";

  import {getHomeMultidata, getHomeGoods} from "network/home";
  import {debounce} from "common/utils";
  import {itemListenerMixin} from "common/mixin";


  export default {
    name: "Home",
    components: {
      Scroll,
      NavBar,
      HomeSwiper,
      HomeRecommendView,
      HomeFeaturView,
      TabControl,
      GoodsList,
      BackTop
    },
    data() {
      return {
        banners: [],
        recommends: [],

        goods: {
          'pop': {page: 0, list: []},
          'new': {page: 0, list: []},
          'sell': {page: 0, list: []}
        },

        currentType: 'pop',
        isShow: false,
        tabOffsetTop: 0,
        isTabFixed: false,
        saveY: 0,
        itemImgListener: null
      }
    },
    //混入的
    mixins: [itemListenerMixin],
    created() {
      //1.请求多个数据
      this.getHomeMultidata()
      this.getHomeGoods('pop')
      this.getHomeGoods('new')
      this.getHomeGoods('sell')

    },
    activated() {
      this.$refs.scroll.scrollTo(0, this.saveY, 0)
      this.$refs.scroll.refresh()
    },
    deactivated() {
      this.saveY = this.$refs.scroll.getScrollY()
      console.log(this.saveY)

      this.$bus.$off('itemImgLoad', this.itemImgListener)

    },
    mounted() {
      //混入中
    },

    methods: {
      tabClick(index) {
        switch (index) {
          case 0:
            this.currentType = 'pop'
            break
          case 1:
            this.currentType = 'new'
            break
          case 2:
            this.currentType = 'sell'
            break
        }
        this.$refs.tabControl1.currentIndex = index;
        this.$refs.tabControl2.currentIndex = index;
      },
      backTopClick() {
        this.$refs.scroll.scrollTo(0,0,500)
      },
      //监听滚动
      contentScroll(position){
        this.isShow = (-position.y) > 1000
        this.isTabFixed = (-position.y) > this.tabOffsetTop
      },
      toToadMore() {
        this.getHomeGoods(this.currentType);
      },



      getHomeMultidata(){
        getHomeMultidata().then(res => {
          console.log(res);
          this.banners = res.data.banner.list;
          this.recommends = res.data.recommend.list
        })
      },
      getHomeGoods(type) {
        const page = this.goods[type].page + 1;
        getHomeGoods(type, page).then(res => {
          this.goods[type].list.push(...res.data.list);
          this.goods[type].page += 1

          this.$refs.scroll.finishPullUp()
        })
      },

      //轮播图加载完后
      swiperImgLoad() {
        this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop;
        console.log(this.tabOffsetTop)
      }
    }
  }
</script>

<style scoped>
  #home {
    /*padding-top: 44px;*/
    height: 100vh;
    position: relative;
  }

  .home-nav {
    background-color: var(--color-tint);
    color: #fff;

    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    z-index: 9;
  }

  .tab-control {
    position: sticky;
    top: 44px;
    z-index: 9;
  }

  .content {
    overflow: hidden;

    position: absolute;
    top: 44px;
    bottom: 49px;
    left: 0;
    right: 0;
  }
</style>
