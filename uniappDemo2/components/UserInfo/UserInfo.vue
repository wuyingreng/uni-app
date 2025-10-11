<template>
  <view class="userinfo">
    <image :src="obj.avatar" mode="" class="avatar"></image>
    <view class="username">{{ obj.name }}</view>
  </view>
</template>

<script setup>
defineProps({
  obj: {
    type: Object,
    // 对象或数组的默认值
    // 必须从一个工厂函数返回。
    // 该函数接收组件所接收到的原始 prop 作为参数。
    default(rawProps) {
      return { name: "匿名", avatar: "../../static/logo.png" };
    },
  },
});
// 对象类型的默认值
</script>

<style lang="scss" scoped>
.userinfo{
	width: 100%;
	height: 200px;
	background: #ccc;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
	image{
		width: 100px;
		height: 100px;
		border-radius: 50%;
	}
	.username{
		padding:10px 0;
		font-size: 20px;
	}
}
</style>
