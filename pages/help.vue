<script setup lang="ts">
import { ref } from 'vue'

// 常见问题分类
const faqCategories = [
  {
    id: 'account',
    name: '账号相关',
    icon: 'heroicons:user-circle'
  },
  {
    id: 'rent',
    name: '租房服务',
    icon: 'heroicons:home'
  },
  {
    id: 'sale',
    name: '二手交易',
    icon: 'heroicons:shopping-cart'
  },
  {
    id: 'forum',
    name: '论坛社区',
    icon: 'heroicons:chat-bubble-left-right'
  },
  {
    id: 'payment',
    name: '支付问题',
    icon: 'heroicons:credit-card'
  },
  {
    id: 'other',
    name: '其他问题',
    icon: 'heroicons:question-mark-circle'
  }
]

// 常见问题
const faqs = ref([
  {
    category: 'account',
    question: '如何注册账号？',
    answer: '点击网站右上角的"注册"按钮，填写邮箱、密码等信息即可完成注册。'
  },
  {
    category: 'account',
    question: '忘记密码怎么办？',
    answer: '在登录页面点击"忘记密码"，通过邮箱验证后即可重置密码。'
  },
  {
    category: 'rent',
    question: '如何发布租房信息？',
    answer: '登录后点击"发布租房"，填写房源信息、上传图片，提交后等待审核即可。'
  },
  {
    category: 'rent',
    question: '租房信息审核需要多久？',
    answer: '一般情况下，租房信息会在24小时内完成审核。'
  },
  {
    category: 'sale',
    question: '如何发布二手商品？',
    answer: '登录后点击"发布二手"，填写商品信息、上传图片，设置价格后即可发布。'
  },
  {
    category: 'sale',
    question: '二手商品可以议价吗？',
    answer: '是的，您可以通过私信与卖家进行议价。'
  },
  {
    category: 'forum',
    question: '如何发帖？',
    answer: '登录后在论坛页面点击"发帖"，选择分类后即可发布内容。'
  },
  {
    category: 'forum',
    question: '发帖有什么规则？',
    answer: '请遵守社区规则，不得发布违法、违规内容，保持友善的交流氛围。'
  },
  {
    category: 'payment',
    question: '支持哪些支付方式？',
    answer: '目前支持信用卡、PayPal等主流支付方式。'
  },
  {
    category: 'payment',
    question: '支付失败怎么办？',
    answer: '请检查网络连接和支付信息是否正确，如果问题持续，请联系客服。'
  }
])

// 当前选中的分类
const currentCategory = ref('all')

// 筛选后的FAQ列表
const filteredFaqs = computed(() => {
  if (currentCategory.value === 'all') {
    return faqs.value
  }
  return faqs.value.filter(faq => faq.category === currentCategory.value)
})

// 使用指南
const guides = [
  {
    title: '新手入门',
    description: '了解平台基本功能和使用方法',
    icon: 'heroicons:book-open',
    link: '/guide/getting-started'
  },
  {
    title: '租房指南',
    description: '如何发布和查找租房信息',
    icon: 'heroicons:home',
    link: '/guide/renting'
  },
  {
    title: '二手交易',
    description: '安全可靠的二手交易流程',
    icon: 'heroicons:shopping-cart',
    link: '/guide/selling'
  },
  {
    title: '社区规则',
    description: '了解社区规范和发帖规则',
    icon: 'heroicons:document-text',
    link: '/guide/community'
  }
]

// 联系支持表单
const supportForm = ref({
  name: '',
  email: '',
  subject: '',
  message: ''
})

// 提交表单
const submitSupportForm = () => {
  // 这里添加表单验证和提交逻辑
  alert('感谢您的反馈，我们会尽快处理！')
  // 重置表单
  supportForm.value = {
    name: '',
    email: '',
    subject: '',
    message: ''
  }
}
</script>

<template>
  <div class="help-page">
    <Topbar />
    <SearchBar />
    
    <div class="help-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:question-mark-circle" />
          帮助中心
        </h1>
        <p class="page-subtitle">我们随时为您提供帮助</p>
      </div>

      <!-- 快速入口 -->
      <section class="quick-access">
        <div class="search-box">
          <input 
            type="text" 
            placeholder="搜索问题或关键词"
            class="search-input"
          >
          <button class="search-btn">
            <Icon name="heroicons:magnifying-glass" />
          </button>
        </div>
        <div class="contact-info">
          <div class="contact-item">
            <Icon name="heroicons:phone" />
            <span>客服电话：+353 123 456 789</span>
          </div>
          <div class="contact-item">
            <Icon name="heroicons:envelope" />
            <span>邮箱：support@aigouda.com</span>
          </div>
        </div>
      </section>

      <!-- 使用指南 -->
      <section class="guides-section">
        <h2 class="section-title">使用指南</h2>
        <div class="guides-grid">
          <div 
            v-for="guide in guides" 
            :key="guide.title"
            class="guide-card"
          >
            <div class="guide-icon">
              <Icon :name="guide.icon" />
            </div>
            <h3 class="guide-title">{{ guide.title }}</h3>
            <p class="guide-description">{{ guide.description }}</p>
            <NuxtLink :to="guide.link" class="guide-link">
              查看详情
              <Icon name="heroicons:arrow-right" />
            </NuxtLink>
          </div>
        </div>
      </section>

      <!-- 常见问题 -->
      <section class="faq-section">
        <h2 class="section-title">常见问题</h2>
        <div class="faq-categories">
          <button 
            v-for="category in faqCategories" 
            :key="category.id"
            class="category-btn"
            :class="{ active: currentCategory === category.id }"
            @click="currentCategory = category.id"
          >
            <Icon :name="category.icon" />
            {{ category.name }}
          </button>
        </div>
        <div class="faq-list">
          <div 
            v-for="faq in filteredFaqs" 
            :key="faq.question"
            class="faq-item"
          >
            <div class="faq-question">
              <h3>{{ faq.question }}</h3>
              <Icon name="heroicons:chevron-down" />
            </div>
            <div class="faq-answer">
              <p>{{ faq.answer }}</p>
            </div>
          </div>
        </div>
      </section>

      <!-- 联系支持 -->
      <section class="support-section">
        <h2 class="section-title">联系支持</h2>
        <div class="support-content">
          <div class="support-info">
            <div class="info-item">
              <Icon name="heroicons:clock" />
              <div class="info-content">
                <h3>工作时间</h3>
                <p>周一至周五：9:00 - 18:00</p>
                <p>周六至周日：10:00 - 16:00</p>
              </div>
            </div>
            <div class="info-item">
              <Icon name="heroicons:chat-bubble-left-right" />
              <div class="info-content">
                <h3>在线客服</h3>
                <p>工作时间实时在线</p>
                <button class="chat-btn">开始对话</button>
              </div>
            </div>
          </div>
          <form class="support-form" @submit.prevent="submitSupportForm">
            <div class="form-group">
              <label>姓名</label>
              <input 
                v-model="supportForm.name"
                type="text"
                placeholder="请输入您的姓名"
                required
              >
            </div>
            <div class="form-group">
              <label>邮箱</label>
              <input 
                v-model="supportForm.email"
                type="email"
                placeholder="请输入您的邮箱"
                required
              >
            </div>
            <div class="form-group">
              <label>主题</label>
              <input 
                v-model="supportForm.subject"
                type="text"
                placeholder="请输入问题主题"
                required
              >
            </div>
            <div class="form-group">
              <label>问题描述</label>
              <textarea 
                v-model="supportForm.message"
                placeholder="请详细描述您的问题"
                rows="4"
                required
              />
            </div>
            <button type="submit" class="submit-btn">提交</button>
          </form>
        </div>
      </section>
    </div>
  </div>
</template>

<style scoped>
.help-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.help-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px var(--default-padding-x);
}

/* 页面标题 */
.page-header {
  text-align: center;
  margin-bottom: 40px;
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

/* 快速入口 */
.quick-access {
  margin-bottom: 60px;
}

.search-box {
  display: flex;
  gap: 12px;
  max-width: 600px;
  margin: 0 auto 20px;
}

.search-input {
  flex: 1;
  padding: 12px 20px;
  border: 2px solid #eee;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.search-input:focus {
  outline: none;
  border-color: var(--primary-color);
}

.search-btn {
  padding: 0 20px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.search-btn:hover {
  opacity: 0.9;
}

.contact-info {
  display: flex;
  justify-content: center;
  gap: 30px;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
}

.contact-item .icon {
  color: var(--primary-color);
}

/* 使用指南 */
.guides-section {
  margin-bottom: 60px;
}

.section-title {
  font-size: 2rem;
  color: #333;
  margin-bottom: 40px;
  text-align: center;
}

.guides-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 30px;
}

.guide-card {
  background: white;
  border-radius: 16px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.guide-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.guide-icon {
  width: 64px;
  height: 64px;
  margin: 0 auto 20px;
  background: var(--primary-color);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 1.8rem;
}

.guide-title {
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 12px;
}

.guide-description {
  color: #666;
  line-height: 1.6;
  margin-bottom: 20px;
}

.guide-link {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  color: var(--primary-color);
  text-decoration: none;
  font-weight: 500;
  transition: all 0.3s ease;
}

.guide-link:hover {
  gap: 12px;
}

/* 常见问题 */
.faq-section {
  margin-bottom: 60px;
}

.faq-categories {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  margin-bottom: 30px;
}

.category-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background: white;
  border: 1px solid #eee;
  border-radius: 20px;
  color: #666;
  cursor: pointer;
  transition: all 0.3s ease;
}

.category-btn:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.category-btn.active {
  background: var(--primary-color);
  border-color: var(--primary-color);
  color: white;
}

.faq-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.faq-item {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.faq-question {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.faq-question:hover {
  background: #f8f9fa;
}

.faq-question h3 {
  font-size: 1.1rem;
  color: #333;
}

.faq-question .icon {
  color: #666;
  transition: transform 0.3s ease;
}

.faq-item.active .faq-question .icon {
  transform: rotate(180deg);
}

.faq-answer {
  padding: 0 20px;
  max-height: 0;
  overflow: hidden;
  transition: all 0.3s ease;
}

.faq-item.active .faq-answer {
  padding: 0 20px 20px;
  max-height: 200px;
}

.faq-answer p {
  color: #666;
  line-height: 1.6;
}

/* 联系支持 */
.support-section {
  margin-bottom: 60px;
}

.support-content {
  display: grid;
  grid-template-columns: 1fr 2fr;
  gap: 40px;
  background: white;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.support-info {
  display: flex;
  flex-direction: column;
  gap: 30px;
}

.info-item {
  display: flex;
  align-items: flex-start;
  gap: 16px;
}

.info-item .icon {
  width: 40px;
  height: 40px;
  background: var(--primary-color);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 1.2rem;
}

.info-content h3 {
  font-size: 1.1rem;
  color: #333;
  margin-bottom: 8px;
}

.info-content p {
  color: #666;
  line-height: 1.6;
  margin-bottom: 4px;
}

.chat-btn {
  margin-top: 12px;
  padding: 8px 16px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.chat-btn:hover {
  opacity: 0.9;
}

.support-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-group label {
  color: #333;
  font-size: 0.95rem;
}

.form-group input,
.form-group textarea {
  padding: 12px;
  border: 1px solid #eee;
  border-radius: 8px;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: var(--primary-color);
}

.submit-btn {
  padding: 12px 24px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 1rem;
  transition: all 0.3s ease;
  align-self: flex-end;
}

.submit-btn:hover {
  opacity: 0.9;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .help-container {
    padding: 10px;
  }

  .page-title {
    font-size: 2rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }

  .section-title {
    font-size: 1.6rem;
  }

  .search-box {
    flex-direction: column;
  }

  .search-btn {
    width: 100%;
    padding: 12px;
  }

  .contact-info {
    flex-direction: column;
    align-items: center;
    gap: 16px;
  }

  .guides-grid {
    grid-template-columns: 1fr;
  }

  .faq-categories {
    justify-content: center;
  }

  .support-content {
    grid-template-columns: 1fr;
    gap: 30px;
    padding: 20px;
  }

  .support-info {
    gap: 20px;
  }

  .info-item {
    gap: 12px;
  }

  .info-item .icon {
    width: 32px;
    height: 32px;
    font-size: 1rem;
  }

  .info-content h3 {
    font-size: 1rem;
  }

  .form-group input,
  .form-group textarea {
    padding: 10px;
  }

  .submit-btn {
    width: 100%;
  }
}
</style>
