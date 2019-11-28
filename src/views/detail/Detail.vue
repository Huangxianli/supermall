<template>
  <div id="detail">
    <DetailNavBar class="detail-dev" @titleClick="titleClick" ref="nav"></DetailNavBar>
    <Scroll class="content" ref="scroll" @scroll="contentScroll" :probe-type="3">
      <DetailSwiper :topImages="topImages"></DetailSwiper>
      <DetailBaseInfo :goods="goods"></DetailBaseInfo>
      <DetailShopInfo :shop="shop"></DetailShopInfo>
      <DetailGoodsInfo :detail-info="detailInfo" @imageLoad="imageLoad"></DetailGoodsInfo>
      <DetailParamsInfo :param-info="itemParams" ref="params"></DetailParamsInfo>
      <DetailCommentInfo :comment-info="commentInfo" ref="comment"></DetailCommentInfo>
      <GoodsList :goods="recommends" ref="recomment"></GoodsList>
    </Scroll>
    <DetailBottomBar @addCart="addCart"></DetailBottomBar>
  </div>
</template>

<script>
  import DetailNavBar from "./childComps/DetailNavBar";
  import DetailSwiper from "./childComps/DetailSwiper";
  import DetailBaseInfo from "./childComps/DetailBaseInfo";
  import DetailShopInfo from "./childComps/DetailShopInfo";
  import DetailGoodsInfo from "./childComps/DetailGoodsInfo";
  import DetailParamsInfo from "./childComps/DetailParamsInfo";
  import DetailCommentInfo from "./childComps/DetailCommentInfo";
  import DetailBottomBar from "./childComps/DetailBottomBar";

  import Scroll from "components/common/srcoll/Scroll";
  import GoodsList from "components/content/goods/GoodsList";

  import {getDtail, Goods, Shop, getRecommend} from "network/detail";
  import {debounce} from "common/utils";
  import {itemListenerMixin} from "common/mixin";

  export default {
    name: "Detail",
    components: {
      DetailNavBar,
      DetailSwiper,
      DetailBaseInfo,
      DetailShopInfo,
      Scroll,
      DetailGoodsInfo,
      DetailParamsInfo,
      DetailCommentInfo,
      GoodsList,
      DetailBottomBar
    },
    mixins: [itemListenerMixin],
    data() {
      return {
        iid: null,
        topImages: [],
        goods: {},
        shop: {},
        detailInfo: {},
        itemParams: {},
        commentInfo: {},
        recommends: [],
        itemImgListener: null,
        themeTopYs: [],
        getThemeTopY: null,
        currentIndex: 0
      }
    },
    created() {
      this.iid = this.$route.params.iid

      getDtail(this.iid).then(res => {
        console.log('商品详情业信息')
        console.log(res)
        const data = res.result
        this.topImages = data.itemInfo.topImages

        this.goods = new Goods(data.itemInfo, data.columns, data.shopInfo.services)

        this.shop = new Shop(data.shopInfo)

        this.detailInfo = data.detailInfo

        this.itemParams = data.itemParams

        if (data.rate.cRate !== 0) {
          this.commentInfo = data.rate.list[0]
        }

        // this.$nextTick(() => {
        //   this.themeTopYs = []
        //   this.themeTopYs.push(0)
        //   this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        //   this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        //   this.themeTopYs.push(this.$refs.recomment.$el.offsetTop)
        //   console.log(this.themeTopYs)
        // })
      })

      getRecommend().then(res => {
        console.log(res)
        this.recommends = res.data.list
      })

      this.getThemeTopY = debounce(() => {
        this.themeTopYs = []
        this.themeTopYs.push(0)
        this.themeTopYs.push(this.$refs.params.$el.offsetTop)
        this.themeTopYs.push(this.$refs.comment.$el.offsetTop)
        this.themeTopYs.push(this.$refs.recomment.$el.offsetTop)
        console.log(this.themeTopYs)
      })
    },
    methods: {
      imageLoad() {
        this.$refs.scroll.refresh()
        this.getThemeTopY()
      },

      titleClick(index) {
        console.log(index)
        this.$refs.scroll.scrollTo(0, -this.themeTopYs[index], 200)
      },
      contentScroll(position) {
        const positionY = -position.y
        const length = this.themeTopYs.length
        for(let i = 0; i < length; i++) {
          if (this.currentIndex !== i &&((i < length - 1 && positionY >= this.themeTopYs[i] && positionY < this.themeTopYs[i+1]) || (i === length - 1 && positionY >= this.themeTopYs[i])
          )) {
            this.currentIndex = i;
            console.log(i)
            this.$refs.nav.currentIndex = this.currentIndex
          }
        }
      },

      //加入购物车
      addCart() {
        const product = {}
        product.image = this.topImages[0]
        product.title =this.goods.title
        product.desc = this.goods.desc
        product.price = this.goods.newPrice
        product.iid = this.iid


        // this.$store.cartList.push()
        this.$store.dispatch('addCart', product)
      }

    },
    mounted() {
    },
    destroyed() {
      this.$bus.$off('itemImgLoad',this.itemImgListener)
    }

  }
</script>

<style scoped>
  #detail {
    position: relative;
    z-index: 9;
    background-color: #fff;
    height: 100vh;
  }

  .detail-dev {
    position: relative;
    z-index: 9;
    background-color: #ffffff;
  }

  .content {
    height: calc(100% - 44px - 58px);
  }

</style>
