<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
		
		<!-- 路由API演示入口 -->
		<view class="demo-section">
			<text class="section-title">路由API演示</text>
			<view class="button-group">
				<button @click="goToRouterDemo" class="demo-btn">
					路由API演示
				</button>
				<button @click="goToEventChannelDemo" class="demo-btn">
					EventChannel演示
				</button>
				<button @click="goToWebViewDemo" class="demo-btn">
					WebView演示
				</button>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onLoad() {

		},
		methods: {
			goToRouterDemo() {
				uni.navigateTo({
					url: '/pages/routerDemo/routerDemo'
				})
			},
			goToEventChannelDemo() {
				uni.navigateTo({
					url: '/pages/eventChannelDemo/eventChannelDemo'
				})
			},
			goToWebViewDemo() {
				uni.navigateTo({
					url: '/pages/webViewDemo/webViewDemo'
				})
			}
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
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
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
	
	.demo-section {
		margin-top: 60rpx;
		padding: 30rpx;
		background-color: #f8f9fa;
		border-radius: 10rpx;
		width: 90%;
	}
	
	.section-title {
		font-size: 32rpx;
		font-weight: bold;
		color: #333;
		display: block;
		margin-bottom: 30rpx;
		text-align: center;
	}
	
	.button-group {
		display: flex;
		flex-direction: column;
		gap: 20rpx;
	}
	
	.demo-btn {
		background-color: #007aff;
		color: white;
		border: none;
		padding: 20rpx 40rpx;
		border-radius: 10rpx;
		font-size: 28rpx;
	}
	
	.demo-btn:active {
		background-color: #0056b3;
	}
</style>
