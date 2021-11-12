<template>
  <view>
    <view class="goods_list">
      <view v-for="(goods, i) in goodsList" :key='i' @click="gotoDetail(goods)">
      <my-goods :goods="goods"></my-goods>
      </view>
    </view>
  </view>
</template>

<script>
  export default {
    data() {
      return {
        // 请求参数对象
        queryObj: {
          // 查询关键词
          query: '',
          // 商品分类Id
          cid: '',
          // 页码值
          pagenum: 1,
          // 每页显示多少条数据.
          pagesize: 10
        },
        // 商品列表的数据
        goodsList: [],
        // 总数量，用来实现分页
        total: 0,
        // 是否正在请求数据
        isloading: false
      };
    },
    onLoad(options) {
      // 将页面参数转存到 this.queryObj 对象中
      this.queryObj.query = options.query || ''
      this.queryObj.cid = options.cid || ''

      // 调用获取商品列表数据的方法
      this.getGoodsList()
    },
    methods: {
      async getGoodsList(cb) {
        // 打开节流阀
        this.isloading = true
        const {
          data: res
        } = await uni.$http.get('/api/public/v1/goods/search', this.queryObj)
        // 请求结束后关闭节流阀
        this.isloading = false
        cb && cb()
        if (res.meta.status !== 200) return uni.$showMsg()
        
        // 上拉加载更多。
        // 把旧的数据和新的数据拼接一下
        this.goodsList = [...this.goodsList, ...res.message.goods]
        this.total = res.message.total
        uni.$showMsg('获取数据成功')
      },
      gotoDetail(goods) {
        uni.navigateTo({
          url: '../goods_detail/goods_detail?goods_id=' + goods.goods_id
        })
      }
    },
    onReachBottom() {
      // 判断页码上的信息是否全部加载完毕
      if(this.queryObj.pagenum * this.queryObj.pagesize >= this.total) return uni.$showMsg('数据全部加载完毕!')
      // 判断是否正在请求数据
      if(this.isloading) return
      // 上拉加载更多.
      // 让页码值自增+1
      this.queryObj.pagenum++
      this.getGoodsList()
    },
    onPullDownRefresh() {
      // 重置关键数据
      this.queryObj.pagenum = 1
      this.total = 0
      this.isloading = false
      this.goodsList = []
      // 重新发起数据请求
      this.getGoodsList(() => uni.stopPullDownRefresh())
    }
  }
</script>

<style lang="scss">

</style>
