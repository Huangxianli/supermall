<template>
  <div id ="app">
   <keep-alive exclude="Detail">
     <router-view/>
   </keep-alive>
    <mainTabBar></mainTabBar>
  </div>
</template>

<script>
  import MainTabBar from "components/content/mainTabbar/MainTabBar"
  export default {
    name: "App",
    components: {
     MainTabBar
    }
  }
</script>
<style scoped>
  @import "assets/css/base.css";
</style>
