<template>
  <view class="container">
    <view class="section">
      <text class="title">路由API演示</text>
      <text class="subtitle">演示不同平台下页面跳转的限制</text>
    </view>

    <!-- 问题一：不同平台的页面跳转限制 -->
    <view class="section">
      <text class="section-title">问题一：页面跳转限制</text>

      <view class="button-group">
        <button @click="navigateToRegistered" class="demo-btn">
          跳转到已注册页面
        </button>

        <button @click="openWebUrl" class="demo-btn">打开外部网页</button>

        <button @click="testPreloadPage" class="demo-btn">
          测试预加载页面
        </button>
      </view>

      <view class="info-box">
        <text class="info-title">平台限制说明：</text>
        <text class="info-text">• H5: 可使用 uni.navigateTo + window.open</text>
        <text class="info-text">• 小程序: 只能使用 web-view 组件</text>
        <text class="info-text">
          • App: 可使用 plus.runtime.openURL + web-view
        </text>
      </view>
    </view>

    <!-- 问题二：reLaunch 与 preloadPage -->
    <view class="section">
      <text class="section-title">问题二：reLaunch 与 preloadPage</text>

      <view class="button-group">
        <button @click="preloadTargetPage" class="demo-btn">
          预加载目标页面
        </button>

        <button @click="reLaunchToPreloaded" class="demo-btn">
          reLaunch到预加载页面
        </button>

        <button @click="reLaunchToNormal" class="demo-btn">
          reLaunch到普通页面
        </button>
      </view>

      <view class="info-box">
        <text class="info-title">reLaunch 行为：</text>
        <text class="info-text">• 普通页面：关闭所有页面，重新打开</text>
        <text class="info-text">• 预加载页面：仅触发 onHide，不关闭</text>
      </view>
    </view>

    <!-- 问题三：EventChannel 支持范围 -->
    <view class="section">
      <text class="section-title">问题三：EventChannel 支持范围</text>

      <view class="button-group">
        <button @click="navigateWithEventChannel" class="demo-btn">
          navigateTo + EventChannel
        </button>

        <button @click="redirectWithoutEventChannel" class="demo-btn">
          redirectTo (不支持EventChannel)
        </button>

        <button @click="reLaunchWithoutEventChannel" class="demo-btn">
          reLaunch (不支持EventChannel)
        </button>
      </view>

      <view class="info-box">
        <text class="info-title">EventChannel 支持：</text>
        <text class="info-text">✅ uni.navigateTo</text>
        <text class="info-text">❌ uni.redirectTo</text>
        <text class="info-text">❌ uni.reLaunch</text>
        <text class="info-text">❌ uni.switchTab</text>
        <text class="info-text">❌ uni.navigateBack</text>
      </view>
    </view>

    <!-- 日志显示区域 -->
    <view class="section">
      <text class="section-title">操作日志</text>
      <view class="log-container">
        <text v-for="(log, index) in logs" :key="index" class="log-item">
          {{ log }}
        </text>
      </view>
      <button @click="clearLogs" class="clear-btn">清空日志</button>
    </view>
  </view>
</template>

<script setup>
import { ref } from "vue";

const logs = ref([]);

// 添加日志
const addLog = (message) => {
  const timestamp = new Date().toLocaleTimeString();
  logs.value.unshift(`[${timestamp}] ${message}`);
  if (logs.value.length > 20) {
    logs.value.pop();
  }
};

// 清空日志
const clearLogs = () => {
  logs.value = [];
};

onUnload(() => {
  console.log("router demo 页面卸载");
});
// 问题一：跳转到已注册页面
const navigateToRegistered = () => {
  addLog("尝试跳转到已注册页面...");
  uni.navigateTo({
    url: "/pages/eventChannelDemo/eventChannelDemo?from=routerDemo",
    success: () => {
      addLog("✅ 跳转成功：已注册页面");
    },
    fail: (err) => {
      addLog(`❌ 跳转失败：${err.errMsg}`);
    },
  });
};

// 问题一：打开外部网页
const openWebUrl = () => {
  addLog("尝试打开外部网页...");

  // #ifdef H5
  addLog("H5平台：使用 window.open");
  window.open("https://uniapp.dcloud.net.cn", "_blank");
  // #endif

  // #ifdef MP-WEIXIN
  addLog("小程序平台：使用 web-view 组件");
  uni.showModal({
    title: "提示",
    content: "小程序平台需要使用 web-view 组件打开外部网页",
    showCancel: false,
  });
  // #endif

  // #ifdef APP-PLUS
  addLog("App平台：使用 plus.runtime.openURL");
  plus.runtime.openURL("https://uniapp.dcloud.net.cn");
  // #endif
};

// 问题一：测试预加载页面
const testPreloadPage = () => {
  addLog("预加载目标页面...");
  uni.preloadPage({
    url: "/pages/webViewDemo/webViewDemo",
    success: () => {
      addLog("✅ 预加载成功");
    },
    fail: (err) => {
      addLog(`❌ 预加载失败：${err.errMsg}`);
    },
  });
};

// 问题二：预加载目标页面
const preloadTargetPage = () => {
  addLog("开始预加载目标页面...");
  uni.preloadPage({
    url: "/pages/webViewDemo/webViewDemo?preloaded=true",
    success: () => {
      addLog("✅ 目标页面预加载完成");
    },
    fail: (err) => {
      addLog(`❌ 预加载失败：${err.errMsg}`);
    },
  });
};

// 问题二：reLaunch到预加载页面
const reLaunchToPreloaded = () => {
  addLog("reLaunch到预加载页面...");
  uni.reLaunch({
    url: "/pages/webViewDemo/webViewDemo?preloaded=true",
    success: () => {
      addLog("✅ reLaunch成功（预加载页面仅触发onHide）");
    },
    fail: (err) => {
      addLog(`❌ reLaunch失败：${err.errMsg}`);
    },
  });
};

// 问题二：reLaunch到普通页面
const reLaunchToNormal = () => {
  addLog("reLaunch到普通页面...");
  uni.reLaunch({
    url: "/pages/eventChannelDemo/eventChannelDemo?relaunch=true",
    success: () => {
      addLog("✅ reLaunch成功（普通页面会关闭所有页面）");
    },
    fail: (err) => {
      addLog(`❌ reLaunch失败：${err.errMsg}`);
    },
  });
};

// 问题三：使用EventChannel的navigateTo
const navigateWithEventChannel = () => {
  addLog("使用 navigateTo + EventChannel...");
  uni.navigateTo({
    url: "/pages/eventChannelDemo/eventChannelDemo?withEventChannel=true",
    events: {
      // 监听目标页面发送的事件
      fromTargetPage: (data) => {
        addLog(`📨 收到目标页面消息：${JSON.stringify(data)}`);
      },
    },
    success: (res) => {
      addLog("✅ navigateTo成功，EventChannel已建立");
      // 向目标页面发送消息
      setTimeout(() => {
        res.eventChannel.emit("fromSourcePage", {
          message: "Hello from routerDemo!",
          timestamp: Date.now(),
        });
        addLog("📤 已向目标页面发送消息");
      }, 1000);
    },
    fail: (err) => {
      addLog(`❌ navigateTo失败：${err.errMsg}`);
    },
  });
};

// 问题三：redirectTo（不支持EventChannel）
const redirectWithoutEventChannel = () => {
  addLog("使用 redirectTo（不支持EventChannel）...");
  uni.redirectTo({
    url: "/pages/eventChannelDemo/eventChannelDemo?redirect=true",
    // 注意：redirectTo 不支持 events 参数
    success: () => {
      addLog("✅ redirectTo成功（但无法使用EventChannel）");
    },
    fail: (err) => {
      addLog(`❌ redirectTo失败：${err.errMsg}`);
    },
  });
};

// 问题三：reLaunch（不支持EventChannel）
const reLaunchWithoutEventChannel = () => {
  addLog("使用 reLaunch（不支持EventChannel）...");
  uni.reLaunch({
    url: "/pages/eventChannelDemo/eventChannelDemo?relaunch=true",
    // 注意：reLaunch 不支持 events 参数
    success: () => {
      addLog("✅ reLaunch成功（但无法使用EventChannel）");
    },
    fail: (err) => {
      addLog(`❌ reLaunch失败：${err.errMsg}`);
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

.info-box {
  background-color: #f8f9fa;
  padding: 15px;
  border-radius: 6px;
  border-left: 4px solid #007aff;
}

.info-title {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  display: block;
  margin-bottom: 8px;
}

.info-text {
  font-size: 12px;
  color: #666;
  display: block;
  margin-bottom: 4px;
  line-height: 1.4;
}

.log-container {
  background-color: #1e1e1e;
  color: #ffffff;
  padding: 15px;
  border-radius: 6px;
  max-height: 200px;
  overflow-y: auto;
  margin-bottom: 10px;
}

.log-item {
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
</style>
