<template>
  <view>
    <view class="my-search-container">
      <view class="my-search-box">
        <!-- 使用uni-ui 提供的图标组件 -->
        <uni-icons type="search" size="17"></uni-icons>
        <text class="placeholder">搜索</text>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    name: "my-search",
    data() {
      return {

      };
    }
  }
</script>

<style lang="scss">
  .my-search-container {
    height: 50px;
    background-color: #C00000;
    display: flex;
    align-items: center;
    padding: 0 10px;

    .my-search-box {
      height: 36px;
      background-color: #FFFFFF;
      border-radius: 18px;
      width: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 15px;
      .placeholder {
        margin-left: 5px;
      }
    }
  }
</style>
