<template>
  <view class="out">
    <view v-for="(item, index) in items" :key="index" class="item">
      <checkbox />
      <view class="title">{{ item.name }}</view>
      <view class="del" @click="del(index)">删除</view>
    </view>
  </view>
</template>

<script setup lang="ts">
import { ref } from "vue";
const items = ref([
  {
    id: 11,
    name: "小米",
  },
  {
    id: 22,
    name: "华为",
  },
  {
    id: 33,
    name: "oppo",
  },
  {
    id: 44,
    name: "苹果",
  },
]);
const del = (index: number) => {
  items.value.splice(index, 1);
};
</script>

<style lang="scss" scoped>
.out {
  padding: 10px;
  .item {
    display: flex;
    padding: 10px 0;
    .del {
      color: #c00;
      margin-left: 30px;
      font-size: 12px;
    }
  }
}
</style>
