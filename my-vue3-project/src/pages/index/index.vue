<template>
  <view class="content">
    <z-paging ref="paging" v-model="dataList" @query="queryList">
      <template #top>
        <view @click="search"> 搜索</view>
      </template>
      <view class="item" v-for="(item, index) in dataList" :key="index">
        <view class="item-title">{{ item }}</view>
      </view>
    </z-paging>
  </view>
</template>

<script>
export default {
  data() {
    return {
      dataList: [],
    };
  },
  methods: {
    queryList(pageNo, pageSize) {
      console.log(
        "this.$refs===>",
        this.$refs,
        "pageNo==>",
        pageNo,
        "pageSize==>",
        pageSize
      );
      // 此处请求仅为演示，请替换为自己项目中的请求
      setTimeout(() => {
        // complete的判断逻辑是服务端有返回的数据.length=pageSize
        this.$refs.paging.complete([
          1, 2, 3, 5, 6, 9, 10, 1, 2, 3, 5, 6, 9, 10, 1, 2, 3, 5, 6, 9, 10, 1,
          2, 3, 5, 6, 9, 10,
        ]);
      });
    },
    search() {
      console.log("this.$refs===>", this.$refs);
      this.$refs.paging.refresh();
    },
  },
};
</script>

<style scoped>
.content {
  width: 750rpx;
  height: 100vh;
  background-color: red;
  padding-bottom: env(safe-area-inset-bottom);
}
.item {
  border: 1px solid #ccc;
  height: 100rpx;
}
</style>
