<template>
  <div class="wrapper" ref="wrapper">
    <div class="content">
      <slot></slot>
    </div>
  </div>
</template>

<script>
  import BScroll from 'better-scroll'
  export default {
    name: "Scroll",
    data() {
      return {
        scroll: null
      }
    },
    props: {
      probeType: {
        type: Number,
        default: 0
      },
      pullUpLoad: {
        type: Boolean,
        default: false
      }
    },
    methods: {
      scrollTo(x, y, time = 500){
        this.scroll && this.scroll.scrollTo(x, y, time)
      },
      finishPullUp(){
        this.scroll.finishPullUp()
      },
      refresh() {
        console.log('---')
        this.scroll && this.scroll.refresh()
      },
      getScrollY() {
        return this.scroll ? this.scroll.y : 0
      }

    },

    mounted() {
      this.scroll = new BScroll(this.$refs.wrapper, {
        click: true,
        probeType: this.probeType,
        pullUpLoad: this.pullUpLoad
      })


      if (this.probeType === 2 || this.probeType === 3){
        this.scroll.on("scroll", (position) => {
          //console.log(position);
          this.$emit('scroll', position)
        })
      }

      if (this.pullUpLoad){
        this.scroll.on('pullingUp',() => {
          this.$emit('loadMore')
        })
      }

    }
  }
</script>

<style scoped>

</style>
