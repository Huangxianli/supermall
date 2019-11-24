<template>
  <Swiper>
    <SwiperItem v-for="item in banners">
      <a :href="item.link">
        <img :src="item.image" alt="" @load="imgLoad">
      </a>
    </SwiperItem>
  </Swiper>
</template>

<script>
  import {Swiper, SwiperItem} from "components/common/swiper"
  import {flag} from "../../../../../课堂代码/12-前端模块化/02-ES6的模块化实现/aaa";

  export default {
    name: "HomeSwiper",
    data() {
      return {
        isLoad: false
      }
    },
    components: {
      Swiper,
      SwiperItem
    },
    props: {
      banners: {
        type: Array,
        default() {
          return []
        }
      }
    },
    methods: {
      imgLoad() {
        if (!this.isLoad){
          this.$emit('swiperImgLoad')
          this.isLoad = true
        }

      }
    }

  }
</script>

<style scoped>

</style>
