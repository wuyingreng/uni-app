<template>
  <view class="out">
    <checkbox-group @change="checkboxChange">
      <view v-for="(item, index) in items" :key="index" class="item">
        <checkbox :value="item.id" :checked="item.checked" />
        <view class="title">{{ item.name }}</view>
        <view class="price">{{ item.price }}</view>
        <view class="del" @click="del(index)">删除</view>
      </view>
    </checkbox-group>
    <view>
      <view>totalNums:{{ totalNums }}</view>
      <view>totalPrices:{{ totalPrices }}</view>
    </view>
  </view>
</template>

<script setup>
import { ref, computed } from "vue";
const items = ref([
  {
    id: "11",
    name: "小米",
    price: 300,
    checked: false,
  },
  {
    id: "22",
    name: "华为",
    price: 200,
    checked: false,
  },
  {
    id: "33",
    name: "oppo",
    price: 100,
    checked: false,
  },
  {
    id: "44",
    name: "苹果",
    price: 600,
    checked: false,
  },
]);
const selectedGroup = ref([]);
const del = (index) => {
  items.value.splice(index, 1);
};
const checkboxChange = (e) => {
  console.log("e==>", e);
  selectedGroup.value = e.detail.value;
  items.value.forEach((item) => {
    item.checked = e.detail.value.includes(item.id);
  });
};
const totalNums = computed(() => selectedGroup.value.length);
const totalPrices = computed(() => {
  return items.value
    .filter((item) => item.checked === true)
    .reduce((acc, cur) => (acc += cur.price), 0);
});
</script>

<style lang="scss" scoped>
.out {
  padding: 10px;
  .item {
    display: flex;
    padding: 10px 0;
    .price {
      margin-left: 30px;
    }
    .del {
      color: #c00;
      margin-left: 30px;
      font-size: 12px;
    }
  }
}
</style>
