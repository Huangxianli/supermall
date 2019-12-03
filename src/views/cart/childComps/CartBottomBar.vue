<template>
  <div class="bottom-bar">
    <div class="check-content">
      <CheckButton class="check-button" :is-checked="isSelectAll" @click.native="checkClick"></CheckButton>
      <span>全选</span>

      <div class="price">
        合计:{{totalPrice}}
      </div>

      <div class="calculate">
        去计算({{checkedLength}})
      </div>
    </div>
  </div>
</template>

<script>
  import CheckButton from "components/content/checkButton/CheckButton";
  export default {
    name: "CartBottomBar",
    components: {
      CheckButton
    },
    computed: {
      totalPrice() {
        return '￥' + this.$store.state.cartList.filter(item => {
          return item.checked
        }).reduce((preValue, item) => {
          return preValue + item.price * item.count
        },0)
      },
      checkedLength() {
        return this.$store.state.cartList.filter(item => item.checked).length
      },
      isSelectAll() {
        if (this.$store.state.cartList.length === 0) return false
        return !this.$store.state.cartList.find(item => !item.checked)
      }
    },
    methods: {
      checkClick() {
        console.log("-------")
        if (this.isSelectAll){
          this.$store.state.cartList.forEach(item => item.checked = false)
        } else {
          this.$store.state.cartList.forEach(item => item.checked = true)
        }
      }
    }
  }
</script>

<style scoped>
  .bottom-bar {
    height: 40px;
    background-color: #eeeeee;
    position: relative;
    display: flex;
    bottom: 40px;
    line-height: 40px;
  }
  .check-content {
    display: flex;
    align-items: center;
    margin-left: 10px;
    /*width: 60px;*/
  }

  .check-button {
    width: 20px;
    height: 20px;
    line-height: 20px;
    margin-right: 5px;
  }

  .price {
    margin-left: 30px;
    /*flex: 1;*/
  }

  .calculate {
    width: 90px;
    background-color: red;
    color: #eeeeee;
    /*text-align: center;*/
  }
</style>
