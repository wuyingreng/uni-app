<template>
  <view class="page">
    <!-- 自定义导航栏 -->
    <!-- 注意：这里处理了刘海屏适配，内容会显示在刘海屏下方 -->
    <view
      class="custom-nav-bar"
      :style="{
        // 这里用胶囊按钮距离顶部的距离比较好，用statusBarHeight 不太好。因为胶囊按钮距离顶部的还有一段距离
        paddingTop: capsuleButtonInfo.top + 'px',
        height: navBarHeight + 'px',
      }">
      <!-- 导航栏内容区域 -->
      <view
        class="nav-content"
        :style="{
          height: navContentHeight + 'px',
          paddingRight: rightPadding + 'px',
        }">
        <!-- 左侧：订单列表标题 -->
        <view class="nav-left">
          <text class="nav-title">订单列表</text>
        </view>

        <!-- 右侧：翻译功能按钮，需要避开小程序原生胶囊按钮 -->
        <!-- 胶囊按钮是微信小程序原生的，位于右上角，我们需要为它预留空间 -->
        <view
          class="nav-right"
          :style="{ marginRight: capsuleButtonInfo.width + 10 + 'px' }">
          <!-- 翻译功能按钮 -->
          <view class="translate-btn" @click="handleTranslate">
            <text class="translate-icon">🌐</text>
            <text class="translate-text">翻译</text>
          </view>
        </view>
      </view>
    </view>

    <!-- 页面内容区域 -->
    <view class="page-content" :style="{ marginTop: navBarHeight + 'px' }">
      <!-- 订单列表 -->
      <view class="order-list">
        <view
          class="order-item"
          v-for="(order, index) in orderList"
          :key="index">
          <view class="order-header">
            <text class="order-id">订单号：{{ order.id }}</text>
            <text class="order-status" :class="order.status">
              {{ order.statusText }}
            </text>
          </view>
          <view class="order-info">
            <text class="order-title">{{ order.title }}</text>
            <text class="order-price">¥{{ order.price }}</text>
          </view>
          <view class="order-time">
            <text class="time-text">{{ order.time }}</text>
          </view>
        </view>
      </view>

      <!-- 翻译功能演示区域 -->
      <view class="translate-demo" v-if="showTranslateDemo">
        <view class="translate-section">
          <text class="section-title">翻译功能演示</text>
          <view class="translate-input">
            <input
              v-model="translateText"
              placeholder="输入要翻译的文本"
              class="input-field" />
            <button @click="doTranslate" class="translate-action-btn">
              翻译
            </button>
          </view>
          <view class="translate-result" v-if="translateResult">
            <text class="result-label">翻译结果：</text>
            <text class="result-text">{{ translateResult }}</text>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted } from "vue";

// 状态栏高度
const statusBarHeight = ref(0);

// 胶囊按钮信息
const capsuleButtonInfo = ref({
  width: 87,
  height: 32,
  top: 0,
  right: 0,
});

// 导航栏布局计算
const navContentHeight = ref(44); // 导航内容区高度 = 胶囊高度
const rightPadding = ref(10); // 右侧留白 = 屏幕右侧到胶囊右缘的距离 + 间距
const navBarHeight = ref(64); // 整个自定义导航栏高度 = 状态栏 + 内容区

// 订单列表数据
const orderList = ref([
  {
    id: "ORD001",
    title: "iPhone 15 Pro Max",
    price: "9999.00",
    status: "pending",
    statusText: "待付款",
    time: "2024-01-15 14:30",
  },
  {
    id: "ORD002",
    title: "MacBook Pro 16寸",
    price: "18999.00",
    status: "shipped",
    statusText: "已发货",
    time: "2024-01-14 09:15",
  },
  {
    id: "ORD003",
    title: "AirPods Pro 2",
    price: "1899.00",
    status: "delivered",
    statusText: "已送达",
    time: "2024-01-13 16:45",
  },
]);

// 翻译相关
const showTranslateDemo = ref(false);
const translateText = ref("");
const translateResult = ref("");

// 获取系统信息
onMounted(() => {
  // 获取状态栏高度
  uni.getSystemInfo({
    success: (res) => {
      statusBarHeight.value = res.statusBarHeight || 20;

      // #ifdef MP-WEIXIN
      const menuButtonInfo = uni.getMenuButtonBoundingClientRect();
      capsuleButtonInfo.value = {
        width: menuButtonInfo.width,
        height: menuButtonInfo.height,
        top: menuButtonInfo.top,
        right: menuButtonInfo.right,
      };

      // 计算导航栏布局
      navContentHeight.value = menuButtonInfo.height;
      rightPadding.value = res.windowWidth - menuButtonInfo.right + 10;
      // 常用计算：状态栏 + (胶囊top - 状态栏)*2 + 胶囊高度
      navBarHeight.value =
        statusBarHeight.value +
        (menuButtonInfo.top - statusBarHeight.value) * 2 +
        menuButtonInfo.height;

      console.log("menuButtonInfo==>", menuButtonInfo);
      console.log("navContentHeight==>", navContentHeight.value);
      console.log("rightPadding==>", rightPadding.value);
      console.log("navBarHeight==>", navBarHeight.value);
      // #endif
    },
  });
});

// 注意：胶囊按钮是微信小程序原生的，不需要我们处理点击事件
// 胶囊按钮会自动处理返回和回到首页的功能

// 翻译功能

const handleTranslate = () => {
  showTranslateDemo.value = !showTranslateDemo.value;
  if (!showTranslateDemo.value) {
    translateText.value = "";
    translateResult.value = "";
  }
};

const doTranslate = () => {
  if (!translateText.value.trim()) {
    uni.showToast({
      title: "请输入要翻译的文本",
      icon: "none",
    });
    return;
  }

  // 模拟翻译功能
  uni.showLoading({
    title: "翻译中...",
  });

  setTimeout(() => {
    // #ifdef 
    uni.hideLoading();
    // #endif
    // 简单的模拟翻译
    translateResult.value = `[翻译结果] ${translateText.value}`;
    uni.showToast({
      title: "翻译完成",
      icon: "success",
    });
  }, 1500);
};
</script>

<style lang="scss" scoped>
.page {
  min-height: 100vh;
  background-color: #f5f5f5;
}

/* 自定义导航栏样式 */
.custom-nav-bar {
  background-color: #ffffff;
  border-bottom: 1px solid #e5e5e5;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 999;
}

.nav-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-left: 15px;
}

.nav-left {
  flex: 1;
}

.nav-title {
  font-size: 18px;
  font-weight: bold;
  color: #333333;
}

.nav-right {
  display: flex;
  align-items: center;
  gap: 10px;
}

/* 翻译按钮样式 */
.translate-btn {
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 6px 12px;
  background-color: #007aff;
  border-radius: 16px;
}

.translate-icon {
  font-size: 14px;
}

.translate-text {
  font-size: 12px;
  color: #ffffff;
}

/* 页面内容区域 */
.page-content {
  padding: 15px;
}

/* 订单列表样式 */
.order-list {
  background-color: #ffffff;
  border-radius: 8px;
  overflow: hidden;
}

.order-item {
  padding: 15px;
  border-bottom: 1px solid #f0f0f0;
}

.order-item:last-child {
  border-bottom: none;
}

.order-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.order-id {
  font-size: 14px;
  color: #666666;
}

.order-status {
  font-size: 12px;
  padding: 2px 8px;
  border-radius: 10px;

  &.pending {
    background-color: #fff3cd;
    color: #856404;
  }

  &.shipped {
    background-color: #d1ecf1;
    color: #0c5460;
  }

  &.delivered {
    background-color: #d4edda;
    color: #155724;
  }
}

.order-info {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.order-title {
  font-size: 16px;
  color: #333333;
  font-weight: 500;
}

.order-price {
  font-size: 16px;
  color: #ff6b35;
  font-weight: bold;
}

.order-time {
  font-size: 12px;
  color: #999999;
}

/* 翻译演示区域 */
.translate-demo {
  margin-top: 20px;
  background-color: #ffffff;
  border-radius: 8px;
  padding: 15px;
}

.translate-section {
  .section-title {
    font-size: 16px;
    font-weight: bold;
    color: #333333;
    margin-bottom: 15px;
    display: block;
  }
}

.translate-input {
  display: flex;
  gap: 10px;
  margin-bottom: 15px;
}

.input-field {
  flex: 1;
  height: 40px;
  padding: 0 12px;
  border: 1px solid #e5e5e5;
  border-radius: 6px;
  font-size: 14px;
}

.translate-action-btn {
  padding: 0 20px;
  height: 40px;
  background-color: #007aff;
  color: #ffffff;
  border: none;
  border-radius: 6px;
  font-size: 14px;
}

.translate-result {
  padding: 12px;
  background-color: #f8f9fa;
  border-radius: 6px;
  border-left: 4px solid #007aff;
}

.result-label {
  font-size: 12px;
  color: #666666;
  display: block;
  margin-bottom: 5px;
}

.result-text {
  font-size: 14px;
  color: #333333;
  line-height: 1.5;
}
</style>
