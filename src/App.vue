<script setup lang="ts">
import { ref, onMounted, computed } from "vue";
import RainBackground from "./components/RainBackground.vue";

// 加载状态管理
const isLoaded = ref(false);
// 二维码显示状态
const showQRCode = ref(false);
const qrCodeType = ref("");
// 语言检测
const isEnglish = ref(false);

// 检测浏览器语言
const detectLanguage = () => {
  const browserLang = navigator.language || navigator.languages[0];
  // 优先英文，非中文都显示英文
  isEnglish.value = !browserLang.startsWith("zh");
};

// 多语言内容
const content = computed(() => {
  if (isEnglish.value) {
    return {
      name: "LN",
      description:
        "Code is poetry, and I am the poet who never stops creating.",
      subtitle: "《Philosopher》—— LN",
      copyright: "© 2025 LN · China",
      links: {
        github: "GitHub",
        qq: "QQ",
        wechat: "WeChat",
      },
      modal: {
        qqTitle: "QQ Contact",
        wechatTitle: "WeChat Contact",
        qqLabel: "QQ Number",
        wechatLabel: "WeChat ID",
        qqTip: "Copy number to add friend",
        wechatTip: "Copy WeChat ID to add friend",
        qqNumber: "945967063",
        wechatId: "DQPLN0617",
      },
    };
  } else {
    return {
      name: "LN",
      description: "不折腾会死，我是这样子，我的代码也是这样子。",
      subtitle: "《哲学家》—— 维子",
      copyright: "© 2025 LN · China",
      links: {
        github: "GitHub",
        qq: "QQ",
        wechat: "微信",
      },
      modal: {
        qqTitle: "QQ 联系方式",
        wechatTitle: "微信联系方式",
        qqLabel: "QQ 号码",
        wechatLabel: "微信号",
        qqTip: "复制号码添加好友",
        wechatTip: "复制微信号添加好友",
        qqNumber: "945967063",
        wechatId: "DQPLN0617",
      },
    };
  }
});

// 页面加载完成后触发动画
onMounted(() => {
  detectLanguage();
  setTimeout(() => {
    isLoaded.value = true;
  }, 100);
});

// 显示二维码
const showQR = (type: string) => {
  qrCodeType.value = type;
  showQRCode.value = true;
};

// 隐藏二维码
const hideQR = () => {
  showQRCode.value = false;
  qrCodeType.value = "";
};
</script>

<template>
  <div
    class="minimal-page"
    :class="{ loaded: isLoaded }"
    :lang="isEnglish ? 'en' : 'zh'"
  >
    <!-- 细雨背景 -->
    <RainBackground />
    <!-- 加载动画 -->
    <div class="loading-overlay" :class="{ 'fade-out': isLoaded }">
      <div class="loading-content">
        <div class="loading-dots">
          <span></span>
          <span></span>
          <span></span>
        </div>
        <p class="loading-text">LN</p>
      </div>
    </div>

    <main class="main-content" :class="{ 'fade-in': isLoaded }">
      <div class="container">
        <!-- 个人信息 -->
        <section class="hero-section">
          <h1 class="name">{{ content.name }}</h1>

          <p class="description">
            {{ content.description }}
          </p>

          <p class="subtitle">{{ content.subtitle }}</p>
        </section>

        <!-- 链接区域 -->
        <section class="links-section">
          <div class="links-grid">
            <a
              href="https://github.com/945967063"
              target="_blank"
              class="link-item"
              >{{ content.links.github }}</a
            >
            <button @click="showQR('qq')" class="link-item link-button">
              {{ content.links.qq }}
            </button>
            <button @click="showQR('weixin')" class="link-item link-button">
              {{ content.links.wechat }}
            </button>
          </div>
        </section>

        <!-- 页脚 -->
        <footer class="footer">
          <p class="copyright">{{ content.copyright }}</p>
        </footer>
      </div>
    </main>

    <!-- 二维码弹窗 -->
    <div v-if="showQRCode" class="qr-overlay" @click="hideQR">
      <div class="qr-modal" @click.stop>
        <div class="qr-header">
          <h3>
            {{
              qrCodeType === "qq"
                ? content.modal.qqTitle
                : content.modal.wechatTitle
            }}
          </h3>
          <button @click="hideQR" class="close-btn">×</button>
        </div>
        <div class="qr-content">
          <div class="contact-info-display">
            <div class="contact-icon">
              {{ qrCodeType === "qq" ? "🐧" : "💬" }}
            </div>
            <div class="contact-number">
              {{
                qrCodeType === "qq"
                  ? content.modal.qqNumber
                  : content.modal.wechatId
              }}
            </div>
            <p class="contact-label">
              {{
                qrCodeType === "qq"
                  ? content.modal.qqLabel
                  : content.modal.wechatLabel
              }}
            </p>
            <small class="contact-tip">
              {{
                qrCodeType === "qq"
                  ? content.modal.qqTip
                  : content.modal.wechatTip
              }}
            </small>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
/* 重置默认样式 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto",
    "Helvetica Neue", Arial, sans-serif;
  line-height: 1.6;
  color: #333;
  background-color: #fff;
}

.minimal-page {
  min-height: 100vh;
  display: flex;
  align-items: flex-start;
  justify-content: center;
  padding: 8rem 2rem 2rem;
  position: relative;
}

/* 加载动画覆盖层 */
.loading-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  transition: opacity 0.8s ease, visibility 0.8s ease;
}

.loading-overlay.fade-out {
  opacity: 0;
  visibility: hidden;
}

.loading-content {
  text-align: center;
}

.loading-dots {
  display: flex;
  justify-content: center;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.loading-dots span {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: #2c3e50;
  animation: loading-bounce 1.4s ease-in-out infinite both;
}

.loading-dots span:nth-child(1) {
  animation-delay: -0.32s;
}
.loading-dots span:nth-child(2) {
  animation-delay: -0.16s;
}
.loading-dots span:nth-child(3) {
  animation-delay: 0s;
}

@keyframes loading-bounce {
  0%,
  80%,
  100% {
    transform: scale(0.8);
    opacity: 0.5;
  }
  40% {
    transform: scale(1);
    opacity: 1;
  }
}

.loading-text {
  font-size: 1.5rem;
  color: #2c3e50;
  font-weight: 300;
  margin: 0;
  animation: loading-fade 2s ease-in-out infinite;
}

/* 英文字体优化 */
[lang="en"] .description {
  font-family: "Georgia", "Times New Roman", serif;
  font-style: italic;
  line-height: 1.6;
}

[lang="en"] .name {
  letter-spacing: 2px;
}

@keyframes loading-fade {
  0%,
  100% {
    opacity: 0.5;
  }
  50% {
    opacity: 1;
  }
}

/* 主内容淡入动画 */
.main-content {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.8s ease 0.3s, transform 0.8s ease 0.3s;
}

.main-content.fade-in {
  opacity: 1;
  transform: translateY(0);
}

.container {
  max-width: 600px;
  width: 100%;
  text-align: center;
}

/* 个人信息区域 */
.hero-section {
  margin-bottom: 2rem;
  animation: slide-up 0.8s ease 0.6s both;
}

.name {
  font-size: 3rem;
  font-weight: 500;
  margin-top: 2rem;
  margin-bottom: 3rem;
  color: #2c3e50;
  animation: slide-up 0.8s ease 0.8s both;
  font-family: "Georgia", "Times New Roman", serif;
}

.description {
  font-size: 1.1rem;
  color: #666;
  margin-bottom: 0.5rem;
  line-height: 1.8;
  animation: slide-up 0.8s ease 1s both;
}

.subtitle {
  font-size: 0.9rem;
  color: #999;
  font-style: italic;
  animation: slide-up 0.8s ease 1.2s both;
}

@keyframes slide-up {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 链接区域 */
.links-section {
  margin-bottom: 2rem;
  animation: slide-up 0.8s ease 1.4s both;
}

.links-grid {
  display: flex;
  justify-content: center;
  gap: 2rem;
  flex-wrap: wrap;
}

.link-item {
  color: #666;
  text-decoration: none;
  font-size: 0.95rem;
  padding: 0.5rem 0;
  border-bottom: 1px solid transparent;
  transition: all 0.3s ease;
  opacity: 0;
  animation: fade-in-stagger 0.6s ease forwards;
}

.link-item:nth-child(1) {
  animation-delay: 1.6s;
}
.link-item:nth-child(2) {
  animation-delay: 1.8s;
}
.link-item:nth-child(3) {
  animation-delay: 2s;
}

/* 按钮样式链接 */
.link-button {
  background: none;
  border: none;
  cursor: pointer;
  font-family: inherit;
}

.link-item:hover {
  color: #2c3e50;
  border-bottom-color: #2c3e50;
  transform: translateY(-2px);
}

@keyframes fade-in-stagger {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 页脚 */
.footer {
  border-top: 1px solid #eee;
  padding-top: 2rem;
  animation: slide-up 0.8s ease 2.4s both;
}

.copyright {
  font-size: 0.85rem;
  color: #999;
}

/* 二维码弹窗样式 */
.qr-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  animation: fade-in 0.3s ease;
}

.qr-modal {
  background: white;
  border-radius: 12px;
  padding: 0;
  max-width: 320px;
  width: 90%;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.15);
  animation: modal-slide-up 0.3s ease;
}

.qr-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1.5rem;
  border-bottom: 1px solid #eee;
}

.qr-header h3 {
  margin: 0;
  font-size: 1.1rem;
  color: #2c3e50;
}

.close-btn {
  background: none;
  border: none;
  font-size: 1.5rem;
  color: #999;
  cursor: pointer;
  padding: 0;
  width: 30px;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  transition: all 0.2s ease;
}

.close-btn:hover {
  background-color: #f5f5f5;
  color: #666;
}

.qr-content {
  padding: 2rem 1.5rem 1.5rem;
  text-align: center;
}

.contact-info-display {
  padding: 1rem;
}

.contact-icon {
  font-size: 4rem;
  margin-bottom: 1rem;
}

.contact-number {
  font-size: 1.5rem;
  font-weight: 600;
  color: #2c3e50;
  margin-bottom: 0.5rem;
  padding: 0.75rem 1rem;
  background-color: #f8f9fa;
  border-radius: 8px;
  border: 1px solid #e9ecef;
  font-family: "Courier New", monospace;
  letter-spacing: 1px;
  user-select: all;
  cursor: pointer;
  transition: all 0.2s ease;
}

.contact-number:hover {
  background-color: #e9ecef;
  border-color: #2c3e50;
}

.contact-label {
  color: #666;
  font-size: 1rem;
  margin: 0.5rem 0;
  font-weight: 500;
}

.contact-tip {
  color: #999;
  font-size: 0.85rem;
}

@keyframes fade-in {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

@keyframes modal-slide-up {
  from {
    opacity: 0;
    transform: translateY(30px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .minimal-page {
    padding: 4rem 1rem 2rem;
    align-items: center;
  }

  .name {
    font-size: 2.5rem;
  }

  .links-grid {
    gap: 1.5rem;
  }

  .link-item {
    font-size: 0.9rem;
  }
}

@media (max-width: 480px) {
  .minimal-page {
    padding: 3rem 1rem 2rem;
  }

  .name {
    font-size: 2rem;
  }

  .description {
    font-size: 1rem;
  }

  .links-grid {
    flex-direction: column;
    gap: 1rem;
  }

  .qr-modal {
    max-width: 280px;
  }

  .contact-number {
    font-size: 1.2rem;
    padding: 0.5rem 0.75rem;
  }
}
</style>
