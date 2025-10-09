<template>
  <view>
    firstname:
    <input v-model="firstname" />
  </view>
  <view>
    lastname:
    <input v-model="lastname" />
  </view>
  <view>fullname1:{{ fullname }}</view>
  <view>fullname2:{{ fullname }}</view>
  <view>fullname3:{{ fullname }}</view>
  <view>fullnameFn1:{{ fullnameFunc() }}</view>
  <view>fullnameFn2:{{ fullnameFunc() }}</view>
  <view>fullnameFn3:{{ fullnameFunc() }}</view>
</template>

<script setup>
import { computed, ref } from "vue";

const firstname = ref("");
const lastname = ref("");
const fullname = computed((previous) => {
  console.log("computed==>");
  if (firstname.value.length <= 10) {
    return `${firstname.value}-${lastname.value}`;
  }
  return previous;
});
const fullnameFunc = () => {
  console.log("fullnameFunc==>");
  return `${firstname.value}-${lastname.value}`;
};
</script>

<style scoped>
input {
  border: 1px solid red;
}
</style>
