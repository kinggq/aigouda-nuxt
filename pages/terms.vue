<script setup lang="ts">
// 服务条款内容
const termsContent = [
  {
    title: '服务说明',
    content: [
      '爱狗达（AIGOUDA）是一个专注于宠物服务的在线平台，提供以下服务：',
      '• 宠物租赁服务',
      '• 二手宠物交易',
      '• 宠物社区互动',
      '• 宠物相关资讯',
      '• 其他相关服务'
    ]
  },
  {
    title: '用户协议',
    content: [
      '使用我们的服务即表示您同意：',
      '• 遵守所有适用的法律法规',
      '• 提供真实、准确的信息',
      '• 保护账户安全',
      '• 遵守社区规则',
      '• 尊重知识产权'
    ]
  },
  {
    title: '账户管理',
    content: [
      '关于账户管理的规定：',
      '• 用户需年满18周岁',
      '• 一个用户只能注册一个账户',
      '• 账户信息需要及时更新',
      '• 禁止转让或授权他人使用',
      '• 平台保留注销违规账户的权利'
    ]
  },
  {
    title: '服务规则',
    content: [
      '使用服务时需要遵守的规则：',
      '• 禁止发布虚假信息',
      '• 禁止骚扰其他用户',
      '• 禁止发布违法内容',
      '• 禁止恶意评价',
      '• 禁止滥用平台功能'
    ]
  },
  {
    title: '知识产权',
    content: [
      '关于知识产权的规定：',
      '• 平台内容的知识产权归平台所有',
      '• 用户发布的内容知识产权归用户所有',
      '• 禁止未经授权使用他人内容',
      '• 平台有权处理侵权内容',
      '• 用户授权平台使用其发布的内容'
    ]
  },
  {
    title: '免责声明',
    content: [
      '平台免责声明：',
      '• 不对用户发布的内容负责',
      '• 不对第三方服务负责',
      '• 不对服务中断负责',
      '• 不对用户损失负责',
      '• 保留修改服务的权利'
    ]
  },
  {
    title: '争议解决',
    content: [
      '争议解决方式：',
      '• 优先通过协商解决',
      '• 协商不成可提交仲裁',
      '• 适用爱尔兰法律',
      '• 仲裁地点在都柏林',
      '• 仲裁费用由败诉方承担'
    ]
  },
  {
    title: '条款修改',
    content: [
      '关于条款修改的规定：',
      '• 平台保留修改条款的权利',
      '• 重大修改会提前通知',
      '• 继续使用即表示接受修改',
      '• 不同意修改可停止使用',
      '• 修改后的条款即时生效'
    ]
  }
]

// 当前选中的章节
const currentSection = ref(0)
</script>

<template>
  <div class="terms-page">
    <Topbar />
    <SearchBar />
    
    <div class="terms-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:document-text" />
          服务条款
        </h1>
        <p class="page-subtitle">最后更新：2024年3月1日</p>
      </div>

      <!-- 主要内容 -->
      <div class="terms-content">
        <!-- 目录导航 -->
        <nav class="terms-nav">
          <div 
            v-for="(section, index) in termsContent" 
            :key="section.title"
            class="nav-item"
            :class="{ active: currentSection === index }"
            @click="currentSection = index"
          >
            <Icon name="heroicons:chevron-right" />
            {{ section.title }}
          </div>
        </nav>

        <!-- 内容展示 -->
        <div class="content-section">
          <div 
            v-for="(section, index) in termsContent" 
            :key="section.title"
            class="section-content"
            :class="{ active: currentSection === index }"
          >
            <h2 class="section-title">{{ section.title }}</h2>
            <div class="section-text">
              <p v-for="(text, i) in section.content" :key="i">{{ text }}</p>
            </div>
          </div>
        </div>
      </div>

      <!-- 底部确认 -->
      <div class="terms-footer">
        <p class="footer-text">
          使用我们的服务即表示您同意这些条款。如果您不同意这些条款，请勿使用我们的服务。
        </p>
        <div class="footer-actions">
          <button class="accept-btn">
            <Icon name="heroicons:check-circle" />
            同意条款
          </button>
          <button class="decline-btn">
            <Icon name="heroicons:x-circle" />
            不同意
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.terms-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.terms-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px var(--default-padding-x);
}

/* 页面标题 */
.page-header {
  text-align: center;
  margin-bottom: 60px;
}

.page-title {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  font-size: 2.5rem;
  color: #333;
  margin-bottom: 16px;
}

.page-subtitle {
  font-size: 1.2rem;
  color: #666;
}

/* 主要内容 */
.terms-content {
  display: grid;
  grid-template-columns: 300px 1fr;
  gap: 40px;
  background: white;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  margin-bottom: 40px;
}

/* 导航菜单 */
.terms-nav {
  position: sticky;
  top: 20px;
  height: fit-content;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 16px;
  border-radius: 8px;
  color: #666;
  cursor: pointer;
  transition: all 0.3s ease;
}

.nav-item:hover {
  background: #f8f9fa;
  color: var(--primary-color);
}

.nav-item.active {
  background: var(--primary-color);
  color: white;
}

.nav-item .icon {
  font-size: 1.2rem;
  transition: transform 0.3s ease;
}

.nav-item.active .icon {
  transform: rotate(90deg);
}

/* 内容区域 */
.content-section {
  position: relative;
}

.section-content {
  display: none;
  animation: fadeIn 0.3s ease;
}

.section-content.active {
  display: block;
}

.section-title {
  font-size: 1.8rem;
  color: #333;
  margin-bottom: 24px;
  padding-bottom: 16px;
  border-bottom: 2px solid #eee;
}

.section-text {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.section-text p {
  color: #666;
  line-height: 1.8;
  font-size: 1.1rem;
}

/* 底部确认 */
.terms-footer {
  background: white;
  border-radius: 16px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.footer-text {
  color: #666;
  font-size: 1.1rem;
  margin-bottom: 30px;
  line-height: 1.6;
}

.footer-actions {
  display: flex;
  justify-content: center;
  gap: 20px;
}

.accept-btn,
.decline-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  border: none;
}

.accept-btn {
  background: var(--primary-color);
  color: white;
}

.decline-btn {
  background: #f8f9fa;
  color: #666;
}

.accept-btn:hover {
  opacity: 0.9;
}

.decline-btn:hover {
  background: #eee;
}

/* 动画 */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .terms-container {
    padding: 10px;
  }

  .page-title {
    font-size: 2rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }

  .terms-content {
    grid-template-columns: 1fr;
    padding: 20px;
    gap: 30px;
  }

  .terms-nav {
    position: static;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    gap: 8px;
  }

  .nav-item {
    padding: 8px 12px;
    font-size: 0.9rem;
  }

  .section-title {
    font-size: 1.5rem;
  }

  .section-text p {
    font-size: 1rem;
  }

  .terms-footer {
    padding: 20px;
  }

  .footer-text {
    font-size: 1rem;
  }

  .footer-actions {
    flex-direction: column;
    gap: 12px;
  }

  .accept-btn,
  .decline-btn {
    width: 100%;
    justify-content: center;
  }
}
</style>
