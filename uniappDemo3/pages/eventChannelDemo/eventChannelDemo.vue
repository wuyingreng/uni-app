<template>
  <view class="container">
    <view class="section">
      <text class="title">EventChannel 演示</text>
      <text class="subtitle">页面间事件通信通道</text>
    </view>

    <!-- 接收到的参数显示 -->
    <view class="section">
      <text class="section-title">页面参数</text>
      <view class="param-box">
        <text class="param-item">来源：{{ pageParams.from || "未知" }}</text>
        <text class="param-item">
          时间戳：{{ pageParams.timestamp || "无" }}
        </text>
        <text class="param-item">
          其他参数：{{ JSON.stringify(pageParams) }}
        </text>
      </view>
    </view>

    <!-- EventChannel 功能演示 -->
    <view class="section">
      <text class="section-title">EventChannel 功能</text>

      <view class="button-group">
        <button @click="sendMessageToSource" class="demo-btn">
          发送消息给源页面
        </button>

        <button @click="sendMultipleMessages" class="demo-btn">
          发送多条消息
        </button>

        <button @click="sendDataMessage" class="demo-btn">发送数据消息</button>
      </view>
    </view>

    <!-- 消息接收区域 -->
    <view class="section">
      <text class="section-title">接收到的消息</text>
      <view class="message-container">
        <text
          v-for="(msg, index) in receivedMessages"
          :key="index"
          class="message-item">
          {{ msg }}
        </text>
        <text v-if="receivedMessages.length === 0" class="no-message">
          暂无消息
        </text>
      </view>
      <button @click="clearMessages" class="clear-btn">清空消息</button>
    </view>

    <!-- 页面生命周期日志 -->
    <view class="section">
      <text class="section-title">页面生命周期</text>
      <view class="lifecycle-container">
        <text
          v-for="(log, index) in lifecycleLogs"
          :key="index"
          class="lifecycle-item">
          {{ log }}
        </text>
      </view>
    </view>

    <!-- 返回按钮 -->
    <view class="section">
      <button @click="goBack" class="back-btn">返回上一页</button>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

const pageParams = ref({});
const receivedMessages = ref([]);
const lifecycleLogs = ref([]);
let eventChannel = null;

// 添加生命周期日志
const addLifecycleLog = (event) => {
  const timestamp = new Date().toLocaleTimeString();
  lifecycleLogs.value.push(`[${timestamp}] ${event}`);
  if (lifecycleLogs.value.length > 10) {
    lifecycleLogs.value.shift();
  }
};

// 页面加载
onMounted(() => {
  addLifecycleLog("onMounted 执行");

  // 获取页面参数
  const pages = getCurrentPages();
  const currentPage = pages[pages.length - 1];
  pageParams.value = currentPage.options || {};

  // 获取 EventChannel
  eventChannel = currentPage.$eventChannel;

  if (eventChannel) {
    addLifecycleLog("EventChannel 已获取");

    // 监听来自源页面的消息
    eventChannel.on("fromSourcePage", (data) => {
      const timestamp = new Date().toLocaleTimeString();
      receivedMessages.value.unshift(
        `[${timestamp}] 收到源页面消息：${JSON.stringify(data)}`
      );
      if (receivedMessages.value.length > 20) {
        receivedMessages.value.pop();
      }
    });

    // 监听一次性消息
    eventChannel.once("oneTimeMessage", (data) => {
      const timestamp = new Date().toLocaleTimeString();
      receivedMessages.value.unshift(
        `[${timestamp}] 收到一次性消息：${JSON.stringify(data)}`
      );
    });

    addLifecycleLog("EventChannel 监听器已设置");
  } else {
    addLifecycleLog(
      "⚠️ EventChannel 不可用（可能通过 redirectTo 或 reLaunch 跳转）"
    );
  }
});

// 页面卸载
onUnmounted(() => {
  addLifecycleLog("onUnmounted 执行");

  // 清理 EventChannel 监听器
  if (eventChannel) {
    eventChannel.off("fromSourcePage");
    eventChannel.off("oneTimeMessage");
    addLifecycleLog("EventChannel 监听器已清理");
  }
  console.log("event channel被卸载了");
});

// 发送消息给源页面
const sendMessageToSource = () => {
  if (eventChannel) {
    const message = {
      type: "greeting",
      content: "Hello from EventChannelDemo!",
      timestamp: Date.now(),
    };

    eventChannel.emit("fromTargetPage", message);
    addLifecycleLog("📤 已发送消息给源页面");
  } else {
    uni.showToast({
      title: "EventChannel 不可用",
      icon: "none",
    });
  }
};

// 发送多条消息
const sendMultipleMessages = () => {
  if (eventChannel) {
    for (let i = 1; i <= 3; i++) {
      setTimeout(() => {
        const message = {
          type: "multiple",
          content: `这是第 ${i} 条消息`,
          index: i,
          timestamp: Date.now(),
        };
        eventChannel.emit("fromTargetPage", message);
        addLifecycleLog(`📤 已发送第 ${i} 条消息`);
      }, i * 500);
    }
  } else {
    uni.showToast({
      title: "EventChannel 不可用",
      icon: "none",
    });
  }
};

// 发送数据消息
const sendDataMessage = () => {
  if (eventChannel) {
    const data = {
      type: "data",
      userInfo: {
        name: "EventChannel Demo",
        id: Math.random().toString(36).substr(2, 9),
      },
      statistics: {
        messageCount: receivedMessages.value.length,
        pageLoadTime: Date.now(),
      },
      timestamp: Date.now(),
    };

    eventChannel.emit("fromTargetPage", data);
    addLifecycleLog("📤 已发送数据消息");
  } else {
    uni.showToast({
      title: "EventChannel 不可用",
      icon: "none",
    });
  }
};

// 清空消息
const clearMessages = () => {
  receivedMessages.value = [];
  addLifecycleLog("消息已清空");
};

// 返回上一页
const goBack = () => {
  uni.navigateBack({
    delta: 1,
    success: () => {
      addLifecycleLog("返回上一页成功");
    },
    fail: (err) => {
      addLifecycleLog(`返回失败：${err.errMsg}`);
    },
  });
};
</script>

<style scoped>
.container {
  padding: 20px;
  background-color: #f5f5f5;
  min-height: 100vh;
}

.section {
  background-color: #ffffff;
  margin-bottom: 20px;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 24px;
  font-weight: bold;
  color: #333;
  display: block;
  margin-bottom: 10px;
}

.subtitle {
  font-size: 14px;
  color: #666;
  display: block;
  margin-bottom: 20px;
}

.section-title {
  font-size: 18px;
  font-weight: bold;
  color: #2b9939;
  display: block;
  margin-bottom: 15px;
}

.param-box {
  background-color: #f8f9fa;
  padding: 15px;
  border-radius: 6px;
  border-left: 4px solid #007aff;
}

.param-item {
  font-size: 14px;
  color: #333;
  display: block;
  margin-bottom: 8px;
  line-height: 1.4;
}

.button-group {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 15px;
}

.demo-btn {
  background-color: #007aff;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 6px;
  font-size: 14px;
}

.demo-btn:active {
  background-color: #0056b3;
}

.message-container {
  background-color: #1e1e1e;
  color: #ffffff;
  padding: 15px;
  border-radius: 6px;
  max-height: 200px;
  overflow-y: auto;
  margin-bottom: 10px;
}

.message-item {
  font-size: 12px;
  font-family: "Courier New", monospace;
  display: block;
  margin-bottom: 4px;
  line-height: 1.4;
  word-break: break-all;
}

.no-message {
  font-size: 14px;
  color: #999;
  display: block;
  text-align: center;
  padding: 20px;
}

.lifecycle-container {
  background-color: #fff3cd;
  color: #856404;
  padding: 15px;
  border-radius: 6px;
  border-left: 4px solid #ffc107;
}

.lifecycle-item {
  font-size: 12px;
  font-family: "Courier New", monospace;
  display: block;
  margin-bottom: 4px;
  line-height: 1.4;
}

.clear-btn {
  background-color: #ff6b35;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 12px;
}

.back-btn {
  background-color: #28a745;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 6px;
  font-size: 14px;
  width: 100%;
}
</style>
