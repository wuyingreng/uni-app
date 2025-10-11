<template>
  <view class="container">
    <view class="section">
      <text class="title">WebView 演示</text>
      <text class="subtitle">不同平台打开外部网页的方式</text>
    </view>

    <!-- 平台检测 -->
    <view class="section">
      <text class="section-title">当前平台</text>
      <view class="platform-info">
        <text class="platform-item">平台：{{ platform }}</text>
        <text class="platform-item">支持方式：{{ supportedMethods }}</text>
      </view>
    </view>

    <!-- WebView 组件演示 -->
    <view class="section">
      <text class="section-title">WebView 组件</text>
      <text class="description">
        适用于所有平台，但小程序需要配置域名白名单
      </text>

      <!-- 小程序平台特殊说明 -->
      <!-- #ifdef MP-WEIXIN -->
      <view class="warning-box">
        <text class="warning-title">⚠️ 小程序平台注意事项：</text>
        <text class="warning-text">
          1. 需要在微信小程序后台配置业务域名白名单
        </text>
        <text class="warning-text">2. 域名必须支持 HTTPS</text>
        <text class="warning-text">3. 需要下载校验文件并上传到域名根目录</text>
        <text class="warning-text">4. 个人小程序不支持 web-view 组件</text>
      </view>
      <!-- #endif -->

      <view class="button-group">
        <button @click="showWebView = !showWebView" class="demo-btn">
          {{ showWebView ? "隐藏" : "显示" }} WebView
        </button>

        <button @click="changeWebViewUrl" class="demo-btn">切换网址</button>

        <!-- #ifdef MP-WEIXIN -->
        <button @click="showMiniProgramConfig" class="demo-btn mp-config-btn">
          查看配置说明
        </button>
        <!-- #endif -->
      </view>

      <!-- 网址选择器 -->
      <view class="url-selector">
        <text class="selector-title">选择要访问的网址：</text>
        <view class="url-list">
          <view
            v-for="(url, index) in webViewUrls"
            :key="index"
            class="url-item"
            :class="{ active: webViewUrl === url.url }"
            @click="selectUrl(url)">
            <text class="url-name">{{ url.name }}</text>
            <text class="url-domain">{{ url.domain }}</text>
          </view>
        </view>
      </view>

      <!-- WebView 组件 -->
      <view v-if="showWebView" class="webview-container">
        <web-view
          :src="webViewUrl"
          @message="onWebViewMessage"
          @error="onWebViewError"
          @load="onWebViewLoad"></web-view>
      </view>
    </view>

    <!-- 平台特定方法 -->
    <view class="section">
      <text class="section-title">平台特定方法</text>

      <view class="button-group">
        <!-- H5 平台 -->
        <!-- #ifdef H5 -->
        <button @click="openWithWindowOpen" class="demo-btn h5-btn">
          window.open (H5)
        </button>
        <!-- #endif -->

        <!-- App 平台 -->
        <!-- #ifdef APP-PLUS -->
        <button @click="openWithPlusRuntime" class="demo-btn app-btn">
          plus.runtime.openURL (App)
        </button>
        <!-- #endif -->

        <!-- 小程序平台 -->
        <!-- #ifdef MP-WEIXIN -->
        <button @click="showMiniProgramTip" class="demo-btn mp-btn">
          小程序限制提示
        </button>
        <!-- #endif -->
      </view>
    </view>

    <!-- 预加载测试 -->
    <view class="section">
      <text class="section-title">预加载测试</text>
      <text class="description">测试 reLaunch 到预加载页面的行为</text>

      <view class="button-group">
        <button @click="testPreloadBehavior" class="demo-btn">
          测试预加载行为
        </button>
      </view>
    </view>

    <!-- 不预加载测试 -->
    <view class="section">
      <text class="section-title">预加载测试</text>
      <text class="description">测试 reLaunch 到预加载页面的行为</text>

      <view class="button-group">
        <button @click="testWithoutPreloadBehavior" class="demo-btn">
          不预加载直接relanch
        </button>
      </view>
    </view>

    <!-- 日志区域 -->
    <view class="section">
      <text class="section-title">操作日志</text>
      <view class="log-container">
        <text v-for="(log, index) in logs" :key="index" class="log-item">
          {{ log }}
        </text>
      </view>
      <button @click="clearLogs" class="clear-btn">清空日志</button>
    </view>

    <!-- 返回按钮 -->
    <view class="section">
      <button @click="goBack" class="back-btn">返回上一页</button>
    </view>
  </view>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

const showWebView = ref(false);
const webViewUrl = ref("https://uniapp.dcloud.net.cn");
const logs = ref([]);
const platform = ref("");
const supportedMethods = ref("");

// 可访问的网址列表（小程序需要配置白名单）
const webViewUrls = ref([
  {
    name: "uni-app 官网",
    url: "https://uniapp.dcloud.net.cn",
    domain: "uniapp.dcloud.net.cn",
    description: "uni-app 官方文档",
  },
  {
    name: "百度",
    url: "https://www.baidu.com",
    domain: "www.baidu.com",
    description: "百度搜索",
  },
  {
    name: "GitHub",
    url: "https://github.com",
    domain: "github.com",
    description: "代码托管平台",
  },
  {
    name: "微信公众平台",
    url: "https://mp.weixin.qq.com",
    domain: "mp.weixin.qq.com",
    description: "微信小程序后台",
  },
]);

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

// 检测平台和支持的方法
onMounted(() => {
  // #ifdef H5
  platform.value = "H5";
  supportedMethods.value = "uni.navigateTo + window.open + web-view";
  // #endif

  // #ifdef MP-WEIXIN
  platform.value = "微信小程序";
  supportedMethods.value = "web-view (需配置域名白名单)";
  // #endif

  // #ifdef APP-PLUS
  platform.value = "App";
  supportedMethods.value = "plus.runtime.openURL + web-view";
  // #endif

  addLog(`页面加载完成，当前平台：${platform.value}`);

  // 检查是否通过预加载进入
  const pages = getCurrentPages();
  const currentPage = pages[pages.length - 1];
  const options = currentPage.options || {};

  if (options.preloaded === "true") {
    addLog("✅ 这是通过预加载进入的页面");
    addLog("reLaunch 到此页面时只会触发 onHide，不会重新加载");
  } else if (options.relaunch === "true") {
    addLog("✅ 这是通过 reLaunch 进入的页面");
    addLog("所有页面被关闭，重新打开此页面");
  } else if (options.redirect === "true") {
    addLog("✅ 这是通过 redirectTo 进入的页面");
    addLog("当前页面被替换");
  } else if (options.withEventChannel === "true") {
    addLog("✅ 这是通过 navigateTo + EventChannel 进入的页面");
    addLog("支持页面间通信");
  }
});

onUnload(() => {
  console.log("webview demo 页面卸载");
});
// 切换 WebView 网址
const changeWebViewUrl = () => {
  const urls = [
    "https://uniapp.dcloud.net.cn",
    "https://www.baidu.com",
    "https://github.com",
    "https://www.zhihu.com",
  ];
  const currentIndex = urls.indexOf(webViewUrl.value);
  const nextIndex = (currentIndex + 1) % urls.length;
  webViewUrl.value = urls[nextIndex];
  addLog(`WebView 网址已切换为：${webViewUrl.value}`);
};

// 选择网址
const selectUrl = (url) => {
  webViewUrl.value = url.url;
  addLog(`选择网址：${url.name} (${url.domain})`);
};

// WebView 消息处理
const onWebViewMessage = (event) => {
  addLog(`WebView 收到消息：${JSON.stringify(event.detail.data)}`);
};

// WebView 错误处理
const onWebViewError = (event) => {
  addLog(`WebView 加载错误：${JSON.stringify(event.detail)}`);

  // #ifdef MP-WEIXIN
  // 小程序平台特殊处理
  if (event.detail.errMsg && event.detail.errMsg.includes("domain")) {
    addLog("❌ 域名未配置白名单，请在微信小程序后台添加业务域名");
    uni.showModal({
      title: "域名未配置",
      content: "该域名未在微信小程序后台配置白名单，请先配置业务域名",
      showCancel: false,
    });
  }
  // #endif
};

// WebView 加载完成
const onWebViewLoad = (event) => {
  addLog(`WebView 加载完成：${webViewUrl.value}`);
};

// H5 平台：使用 window.open
// #ifdef H5
const openWithWindowOpen = () => {
  addLog("H5平台：使用 window.open 打开外部网页");
  const newWindow = window.open("https://uniapp.dcloud.net.cn", "_blank");
  if (newWindow) {
    addLog("✅ window.open 成功");
  } else {
    addLog("❌ window.open 被浏览器阻止");
  }
};
// #endif

// App 平台：使用 plus.runtime.openURL
// #ifdef APP-PLUS
const openWithPlusRuntime = () => {
  addLog("App平台：使用 plus.runtime.openURL 打开外部网页");
  plus.runtime.openURL("https://uniapp.dcloud.net.cn", (error) => {
    if (error) {
      addLog(`❌ plus.runtime.openURL 失败：${error.message}`);
    } else {
      addLog("✅ plus.runtime.openURL 成功");
    }
  });
};
// #endif

// 小程序平台：显示限制提示
// #ifdef MP-WEIXIN
const showMiniProgramTip = () => {
  addLog("小程序平台：显示限制提示");
  uni.showModal({
    title: "小程序限制",
    content:
      "小程序只能使用 web-view 组件打开外部网页，且需要在后台配置域名白名单。\n\n当前演示使用的是 web-view 组件。",
    showCancel: false,
    confirmText: "知道了",
  });
};

// 显示小程序配置说明
const showMiniProgramConfig = () => {
  addLog("显示小程序 web-view 配置说明");
  uni.showModal({
    title: "小程序 web-view 配置步骤",
    content:
      "1. 登录微信小程序后台\n2. 进入 开发 → 开发管理 → 开发设置\n3. 在业务域名中添加要访问的域名\n4. 下载校验文件并上传到域名根目录\n5. 确保域名支持 HTTPS\n\n注意：个人小程序不支持 web-view",
    showCancel: false,
    confirmText: "知道了",
  });
};
// #endif

// 测试预加载行为
const testPreloadBehavior = () => {
  addLog("开始测试预加载行为...");

  // 预加载一个页面
  uni.preloadPage({
    url: "/pages/routerDemo/routerDemo?preloadTest=true",
    success: () => {
      addLog("✅ 页面预加载成功");

      // 延迟 2 秒后 reLaunch 到预加载的页面
      setTimeout(() => {
        addLog("2秒后 reLaunch 到预加载页面...");
        uni.reLaunch({
          url: "/pages/routerDemo/routerDemo?preloadTest=true",
          success: () => {
            addLog("✅ reLaunch 成功（预加载页面仅触发 onHide）");
          },
          fail: (err) => {
            addLog(`❌ reLaunch 失败：${err.errMsg}`);
          },
        });
      }, 2000);
    },
    fail: (err) => {
      addLog(`❌ 预加载失败：${err.errMsg}`);
    },
  });
};

const testWithoutPreloadBehavior = () => {
  addLog("开始测试预加载行为...");

  uni.reLaunch({
    url: "/pages/routerDemo/routerDemo?preloadTest=true",
    success: () => {
      addLog("✅ reLaunch 成功（预加载页面仅触发 onHide）");
    },
    fail: (err) => {
      addLog(`❌ reLaunch 失败：${err.errMsg}`);
    },
  });
};

// 返回上一页
const goBack = () => {
  uni.navigateBack({
    delta: 1,
    success: () => {
      addLog("返回上一页成功");
    },
    fail: (err) => {
      addLog(`返回失败：${err.errMsg}`);
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

.platform-info {
  background-color: #e7f3ff;
  padding: 15px;
  border-radius: 6px;
  border-left: 4px solid #007aff;
}

.platform-item {
  font-size: 14px;
  color: #333;
  display: block;
  margin-bottom: 8px;
  line-height: 1.4;
}

.description {
  font-size: 12px;
  color: #666;
  display: block;
  margin-bottom: 15px;
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

.h5-btn {
  background-color: #28a745;
}

.app-btn {
  background-color: #ffc107;
  color: #333;
}

.mp-btn {
  background-color: #17a2b8;
}

.mp-config-btn {
  background-color: #ffc107;
  color: #333;
}

.warning-box {
  background-color: #fff3cd;
  border: 1px solid #ffeaa7;
  padding: 15px;
  border-radius: 6px;
  margin-bottom: 15px;
}

.warning-title {
  font-size: 14px;
  font-weight: bold;
  color: #856404;
  display: block;
  margin-bottom: 8px;
}

.warning-text {
  font-size: 12px;
  color: #856404;
  display: block;
  margin-bottom: 4px;
  line-height: 1.4;
}

.url-selector {
  margin-bottom: 15px;
}

.selector-title {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  display: block;
  margin-bottom: 10px;
}

.url-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.url-item {
  padding: 12px;
  border: 1px solid #e5e5e5;
  border-radius: 6px;
  background-color: #f8f9fa;
  transition: all 0.2s;
}

.url-item.active {
  border-color: #007aff;
  background-color: #e7f3ff;
}

.url-item:active {
  background-color: #e9ecef;
}

.url-name {
  font-size: 14px;
  font-weight: bold;
  color: #333;
  display: block;
  margin-bottom: 4px;
}

.url-domain {
  font-size: 12px;
  color: #666;
  display: block;
}

.webview-container {
  height: 300px;
  border: 1px solid #ddd;
  border-radius: 6px;
  overflow: hidden;
  margin-top: 15px;
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

.back-btn {
  background-color: #6c757d;
  color: white;
  border: none;
  padding: 12px 20px;
  border-radius: 6px;
  font-size: 14px;
  width: 100%;
}
</style>
