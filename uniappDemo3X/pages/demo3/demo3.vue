<template>
  <view class="">
    <view
      :style="{
        height: customMenuHeight + 'px',
        marginTop: statusBarHeight + 'px',
      }"
      class="custom-naviagte">
      <button>test</button>
      <button>test2</button>
    </view>
    <!-- <button @click="remove">删除一个</button>
    <button @click="clear">删除所有缓存</button> -->
  </view>
</template>

<script setup>
import { onMounted, ref } from "vue";
console.log("setup执行");

let customMenuHeight = ref(0);
let statusBarHeight = ref(0);
uni.setStorageSync("name", "李四");

uni.setStorageSync("arrs", [1, 2, 3, 4]);

let myName = uni.getStorageSync("name");
console.log(myName);

const res = uni.getStorageInfoSync();
console.log(res);

function remove() {
  uni.removeStorageSync("name");
}

function clear() {
  uni.clearStorageSync();
}

onMounted(() => {
  uni.getSystemInfo({
    success: (res) => {
      console.log("inside getSystemIndo success");
      const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
      customMenuHeight.value =
        (menuButtonInfo.top - res.statusBarHeight) * 2 + menuButtonInfo.height;
      statusBarHeight.value = res.statusBarHeight;
    },
  });
});

uni.getSystemInfo({
  success: (res) => {
    console.log("outside get getSystemInfo success");
    const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
    customMenuHeight.value =
      (menuButtonInfo.top - res.statusBarHeight) * 2 + menuButtonInfo.height;
    statusBarHeight.value = res.statusBarHeight;
  },
  fail(err) {
    console.log("outside get getSystemInfo err");
  },
});
</script>

<style lang="scss" scoped>
.custom-naviagte {
  background-color: red;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
