<template>
  <view>
    <!-- 使用自定义的搜索组件 -->
    <my-search></my-search>

    <!-- 左侧的滑动区域 -->
    <view class="scroll-view-container">
      <scroll-view class="left-scroll-view" scroll-y="true" :style="{height: wh + 'px'}">
        <block v-for="(item, i) in cateList" :key="i">
          <view :class="['left-scroll-view-item', i === active ? 'active' : '']" @click="activeChanged(i)">
            {{item.cat_name}}</view>
        </block>

      </scroll-view>
      <!-- 右侧的滑动区域 -->
      <scroll-view scroll-y="true" :style="{height: wh + 'px'}" :scroll-top="scrollTop">
        <view class="cate-lv2" v-for="(item2, i2) in cateLevel2" :key="i2">
          <view class="cat-lv2-title">
            <!-- 二级分类的标题 -->
            /{{item2.cat_name}}/
          </view>
          <!-- 当前二级分类下的三级分类列表 -->
          <view class="cate-lv3-list">
            <!-- 三级分类的item 项 -->
            <view class="cate-lv3-item" v-for="(item3, i3) in item2.children" :key="i3" @click="gotoGoodsList(item3)">
              <!-- 三级分类的图片 -->
              <image :src="item3.cat_icon" mode=""></image>
              <!-- 三级分类的文本 -->
              <text>{{item3.cat_name}}</text>
            </view>
          </view>
        </view>
      </scroll-view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 当前设备可用的高度
        wh: 0,
        cateList: [],
        active: 0,
        cateLevel2: [],
        scrollTop: 0
      };
    },
    onLoad() {
      // 动态获取当前设备的可使用屏幕高度
      const sysInfo = uni.getSystemInfoSync()
      this.wh = sysInfo.windowHeight - 50
      this.getCateList()
      this.getCateLevel()
    },
    methods: {
      async getCateList() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        // console.log(res)
        if (res.meta.status !== 200) return uni.$showMsg()

        this.cateList = res.message
        uni.$showMsg('获取商品信息成功')
      },
      // 动态的改变active的值
      activeChanged(i) {
        this.active = i

        // 重新为二级分类赋值,动态的展示对应的二级分类信息
        this.cateLevel2 = this.cateList[i].children
        
        // 切换一级分类后重置滚动条的位置
        this.scrollTop = this.scrollTop ===0 ? 1 : 0
      },
      async getCateLevel() {
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/categories')
        if (res.meta.status !== 200) return uni.$showMsg()
        // 为二级分类赋值
        this.cateLevel2 = res.message[0].children
        uni.$showMsg('获取二级商品信息成功！')
      },
      // 跳转到商品列表页面
      gotoGoodsList(item) {
        uni.navigateTo({
          url:'/subpkg/goods_list/goods_list?cid=' + item.cat_id
        })
      }
    }
  }
</script>

<style lang="scss">
  .scroll-view-container {
    display: flex;
  }

  .left-scroll-view {
    // 如果要使宽度固定不变,就用px,否则用rpx
    width: 120px;

    .left-scroll-view-item {
      background-color: #F7F7F7;
      line-height: 60px;
      text-align: center;
      font-size: 12px;

      &.active {
        background-color: #FFFFFF;
        position: relative;

        &::before {
          content: ' ';
          display: block;
          width: 3px;
          height: 30px;
          background-color: #C00000;
          position: absolute;
          top: 50%;
          left: 0;
          // 走自身高度的一半
          transform: translateY(-50%);
        }
      }
    }
  }
  
  .cat-lv2-title {
    font-size: 12px;
    font-weight: bold;
    text-align: center;
    padding: 15px 0;
  }
  .cate-lv3-list {
    display: flex;
    // 如果装不下,就让他们自动换行
    flex-wrap: wrap;
    .cate-lv3-item {
      width: 33.33%;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      margin-bottom: 10px;
      image {
        height: 60px;
        width: 60px;
      }
      text {
        font-size: 12px;
      }
    }
  }
</style>
