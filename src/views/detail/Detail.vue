<template>
  <div id="detail">
    <DetailNavBar class="detail-dev"></DetailNavBar>
    <Scroll class="content" ref="scroll">
      <DetailSwiper :topImages="topImages"></DetailSwiper>
      <DetailBaseInfo :goods="goods"></DetailBaseInfo>
      <DetailShopInfo :shop="shop"></DetailShopInfo>
      <DetailGoodsInfo :detail-info="detailInfo" @imageLoad="imageLoad"></DetailGoodsInfo>
      <DetailParamsInfo :param-info="itemParams"></DetailParamsInfo>
      <DetailCommentInfo :comment-info="commentInfo"></DetailCommentInfo>
      <GoodsList :goods="recommends"></GoodsList>
    </Scroll>

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

  import Scroll from "components/common/srcoll/Scroll";
  import GoodsList from "components/content/goods/GoodsList";

  import {getDtail, Goods, Shop, getRecommend} from "network/detail";
  import {debounce} from "common/utils";

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
      GoodsList
    },
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
        itemImgListener: null
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
      })

      getRecommend().then(res => {
        console.log(res)
        this.recommends = res.data.list
      })
    },
    methods: {
      imageLoad() {
        this.$refs.scroll.refresh()
      }
    },
    mounted() {
      let refresh = debounce(this.$refs.scroll.refresh, 50)

      this.itemImgListener = () => {
        refresh()
      }
      this.$bus.$on('itemImgLoad',this.itemImgListener)
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
    height: calc(100% - 44px);
  }



</style>
