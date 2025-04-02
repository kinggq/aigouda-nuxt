<script setup lang="ts">
// 隐私政策内容
const privacyContent = [
  {
    title: '信息收集',
    content: [
      '我们收集的信息包括但不限于：',
      '• 个人基本信息（姓名、邮箱、电话等）',
      '• 账户信息（用户名、密码等）',
      '• 使用数据（浏览记录、搜索历史等）',
      '• 设备信息（IP地址、浏览器类型等）'
    ]
  },
  {
    title: '信息使用',
    content: [
      '我们使用收集的信息用于：',
      '• 提供和改进服务',
      '• 个性化用户体验',
      '• 发送服务通知',
      '• 防止欺诈和滥用',
      '• 遵守法律法规'
    ]
  },
  {
    title: '信息共享',
    content: [
      '我们不会出售您的个人信息。仅在以下情况下可能共享信息：',
      '• 获得您的明确同意',
      '• 遵守法律法规要求',
      '• 保护我们的合法权益',
      '• 与授权合作伙伴共享必要信息'
    ]
  },
  {
    title: '信息安全',
    content: [
      '我们采取多种安全措施保护您的信息：',
      '• 数据加密传输和存储',
      '• 访问控制和权限管理',
      '• 定期安全评估',
      '• 员工保密培训'
    ]
  },
  {
    title: 'Cookie使用',
    content: [
      '我们使用Cookie和类似技术：',
      '• 记住您的偏好设置',
      '• 提供个性化体验',
      '• 分析网站使用情况',
      '• 改进服务质量'
    ]
  },
  {
    title: '用户权利',
    content: [
      '您对个人信息拥有以下权利：',
      '• 访问和查看信息',
      '• 更正或更新信息',
      '• 删除信息',
      '• 撤回同意',
      '• 导出数据'
    ]
  },
  {
    title: '政策更新',
    content: [
      '我们可能不时更新本隐私政策：',
      '• 在网站显著位置发布更新通知',
      '• 重大变更时通过邮件通知',
      '• 更新后继续使用即表示同意'
    ]
  },
  {
    title: '联系我们',
    content: [
      '如果您对本隐私政策有任何疑问：',
      '• 邮箱：privacy@aigouda.com',
      '• 电话：+353 123 456 789',
      '• 地址：都柏林市中心，爱尔兰'
    ]
  }
]

// 当前选中的章节
const currentSection = ref(0)
</script>

<template>
  <div class="privacy-page">
    <Topbar />
    <SearchBar />
    
    <div class="privacy-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:shield-check" />
          隐私政策
        </h1>
        <p class="page-subtitle">最后更新：2024年3月1日</p>
      </div>

      <!-- 主要内容 -->
      <div class="privacy-content">
        <!-- 目录导航 -->
        <nav class="privacy-nav">
          <div 
            v-for="(section, index) in privacyContent" 
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
            v-for="(section, index) in privacyContent" 
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
    </div>
  </div>
</template>

<style scoped>
.privacy-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.privacy-container {
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
.privacy-content {
  display: grid;
  grid-template-columns: 300px 1fr;
  gap: 40px;
  background: white;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

/* 导航菜单 */
.privacy-nav {
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
  .privacy-container {
    padding: 10px;
  }

  .page-title {
    font-size: 2rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }

  .privacy-content {
    grid-template-columns: 1fr;
    padding: 20px;
    gap: 30px;
  }

  .privacy-nav {
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
}
</style>
