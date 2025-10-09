<template>
  <view class="content">
    <uni-rate :value="3" :size="30" color="#ddd" active-color="#ffca3e"></uni-rate>
    <!-- 这里会显示一个五角星，并且点击后会自动亮星 -->
    <image class="logo" src="/static/logo.png"></image>
    <view class="text-area">
      <text class="title">{{ title }}</text>
    </view>
    <view>{{ count }}</view>
    <button @click="addCount">增加count的值</button>
    <view class="outer" hover-class="outer-hover">
      <view
        class="inner"
        hover-class="inner-hover"
        hover-stop-propagation></view>
    </view>
    <text selectable user-select>测试text是否可以选择</text>
  </view>
  <scroll-view direction="horizontal" class="scroll-view" :scroll-x="true">
    <view class="scroll-view-content">test</view>
    <view class="scroll-view-content">test</view>
    <view class="scroll-view-content">test</view>
    <view class="scroll-view-content">test</view>
  </scroll-view>
  <swiper
    class="swiper"
    circular
    autoplay
    indicator-color="yellow"
    indicator-active-color="red"
    indicator-dots>
    <swiper-item class="swiper-item">1</swiper-item>
    <swiper-item class="swiper-item">2</swiper-item>
    <swiper-item class="swiper-item">3</swiper-item>
    <swiper-item class="swiper-item">4</swiper-item>
  </swiper>
  <navigator url="/pages/demo1/demo1">跳转到demo1</navigator>
  <button :loading="true" @click="btnClick">按钮</button>
  <input type="text" confirm-type="search" value="111" />
  <input class="uni-input" maxlength="10" placeholder="最大输入长度为10" />
  <image v-bind:src="picture" />
  <view :class="{ active: isActive, area: 'area' }">字体</view>
  <view
    :class="isActive ? 'active' : 'area'"
    :style="{ fontSize: `${size}px` }">
    字体
  </view>
</template>

<script setup name="Index" lang="ts">
import { ref, nextTick, onUnmounted } from "vue";

const count = ref(0);
const title = ref("标题");
const isActive = ref(false);
const size = ref(20);
const arr = [
  "../../static/pic1.png",
  "../../static/pic2.png",
  "../../static/pic3.webp",
  "../../static/pic4.jpg",
];
const picture = ref(arr[0]);
const btnClick = function () {
  console.log("btn click");
};
const addCount = async () => {
  count.value++; // 数据立即更新

  console.log("数据已更新:", count.value); // 1 - 同步

  // 但 DOM 还没更新
  console.log("DOM 元素内容:", document.querySelector("p").textContent); // 可能是 0

  await nextTick(); // 等待 DOM 更新

  console.log("DOM 已更新:", document.querySelector("p").textContent); // 1 - 异步
};

let i = 0;
const intervalId = setInterval(() => {
  i++;
  picture.value = arr[i % 4];
  isActive.value = !isActive.value;
  console.log("picture.value==>", picture.value);
  size.value++;
}, 1000);

onUnmounted(() => clearInterval(intervalId));
</script>

<style lang="scss">
.content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.area {
  width: 200rpx;
  height: 200rpx;
  background-color: green;
}
.active {
  width: 200rpx;
  height: 200rpx;
  background-color: yellow;
}
.logo {
  height: 200rpx;
  width: 200rpx;
  margin-top: 200rpx;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 50rpx;
}

.text-area {
  border: 1px solid red;
  display: flex;
  justify-content: center;
}

.title {
  font-size: 36rpx;
  color: #8f8f94;
}
.outer {
  width: 200rpx;
  height: 200rpx;
  background-color: #8f8f94;
  &.outer-hover {
    background-color: blue;
  }
}
.inner {
  width: 100rpx;
  height: 100rpx;
  background-color: yellow;
  &.inner-hover {
    background-color: yellowgreen;
  }
}
.scroll-view {
  width: 200rpx;
  white-space: nowrap;
}
.scroll-view-content {
  width: 100rpx;
  display: inline-block;
}
.swiper {
  width: 100vw;
  .swiper-item {
    width: 100vw;
    &:nth-child(2n) {
      background-color: yellow;
    }
    &:nth-child(2n + 1) {
      background-color: green;
    }
  }
}
</style>
