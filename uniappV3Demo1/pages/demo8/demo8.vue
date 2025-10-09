<template>
  <view class="outer">
    <input @focus="isActive = true" @blur="isActive = false" />
    <image
      src="../../static/chicken.gif"
      :class="isActive === true ? 'active' : ''" />
  </view>
  <view>
    <input :value="inputValue" class="input" @input="handleInput" />
  </view>
  <view>预览：{{ inputValue }}</view>
  <input
    v-model="inputValue"
    class="input"
    @confirm="
      () => {
        console.log(111);
      }
    " />
</template>

<script setup>
import { ref } from "vue";
const isActive = ref();
const inputValue = ref("");
// 使用 UniApp 的事件类型
const handleInput = (event) => {
  inputValue.value = event.detail.value;
};
</script>

<style scoped lang="scss">
.outer {
  padding: 20px;
  position: relative;

  input {
    border: 1px solid #eee;
    width: 100%;
    position: relative;
    z-index: 2;
    background-color: #fff;
  }

  image {
    position: absolute;
    top: 0;
    left: calc(50% - 10px);
    width: 20px;
    height: 20px;
    z-index: 1;

    &.active {
      top: 20px;
    }
  }
}
.input {
  border: 1px solid red;
}
</style>
