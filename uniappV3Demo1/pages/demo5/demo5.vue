<template>
	<view v-if="shop">
		京东
	</view>
	<view v-else>
		淘宝
	</view>
	<view v-if="day===1">
		星期一
	</view>
	<view v-else-if="day===2">
		星期二
	</view>
	<view v-else-if="day===3">
		星期三
	</view>
	<view v-else-if="day===4">
		星期四
	</view>
	<view v-else-if="day===5">
		星期五
	</view>
	<view v-else>
		格式错误
	</view>
	<template v-if="shop">
		<view>哈哈</view>
	</template>
</template>

<script setup lang="ts">
	import {ref} from 'vue';
	const shop=ref(true);
	const day=ref(3)
	
</script>

<style scoped>
.box {
  width: 200rpx;
  height: 200rpx;
}
</style>
