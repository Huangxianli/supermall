<template>
  <div v-if="Object.keys(detailInfo).length !== 0" class="goodsInfo">
    <div class="info-desc clear-fix">
      <div class="start">
      </div>
      <div class="desc">{{detailInfo.desc}}</div>
      <div class="end"></div>
    </div>

    <div class="info-key">{{detailInfo.detailImage[0].key}}</div>
    <div class="info-list">
     <img v-for="(item, index) in detailInfo.detailImage[0].list" :key="index"
         :src="item" alt="" @load="imgLoad">
    </div>
  </div>
</template>

<script>
  export default {
    name: "DetailGoodsInfo",
    props: {
      detailInfo: {
        type: Object
      }
    },
    data() {
      return {
        counter: 0,
        imageLength: 0
      }
    },
    methods: {
      imgLoad() {
        if (++this.counter === this.imageLength) {
          this.$emit('imageLoad')
        }
      },
      watch: {
        detailInfo() {
          this.imageLength = this.detailInfo.detailImage[0].list.length
        }
      }
    }

  }
</script>

<style scoped>

  .goodsInfo {
    width: 100%;
    position: relative;
  }

  .start {
    width: 50%;
    background-color: #ff5777;
    margin-top: 5px;
    margin-left: 1%;
    height: 1px;
  }

  .desc {
    margin-top: 5px;
    font-size: 5px;
    line-height: 15px;
    color: #666666;
  }

  .end {
    width: 50%;
    background-color: #ff5777;
    margin-top: 5px;
    height: 1px;
    margin-left: 49%;
  }

  .info-key {
    font-size: 15px;
    margin-top: 5px;
    color: #222222;
  }



  .info-list img {
    width: 100%;
  }
</style>
