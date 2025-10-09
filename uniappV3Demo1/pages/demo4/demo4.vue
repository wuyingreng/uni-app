<template>
	<view>
		<view class="box" :style="{backgroundColor:color}" @click="changeColor">点击可以更改背景色</view>
		<switch  @change="switch1Change" />
		<button :loading="isLoading">按钮</button>
	</view>
</template>

<script setup lang="ts">
	import {ref} from 'vue'
	const color=ref('#002200');
	const isLoading=ref(false)
	const changeColor=()=>{
		const randomNum=String(Math.random()).substring(3,9)
		color.value=`#${randomNum}`
	}
	const switch1Change=(e)=>{
		isLoading.value=e.detail.value
	}
	
</script>

<style scoped>
	.box{
		width: 200rpx;
		height: 200rpx;
	}
	       
</style>
