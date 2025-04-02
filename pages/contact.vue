<script setup lang="ts">
import { ref } from 'vue'

// 联系信息
const contactInfo = {
  address: {
    title: '公司地址',
    content: '都柏林市中心，爱尔兰',
    icon: 'heroicons:map-pin'
  },
  phone: {
    title: '联系电话',
    content: '+353 123 456 789',
    icon: 'heroicons:phone'
  },
  email: {
    title: '电子邮箱',
    content: 'contact@aigouda.com',
    icon: 'heroicons:envelope'
  },
  hours: {
    title: '工作时间',
    content: '周一至周五：9:00 - 18:00',
    icon: 'heroicons:clock'
  }
}

// 社交媒体链接
const socialLinks = [
  {
    name: '微信',
    icon: 'ri:wechat-fill',
    link: '#'
  },
  {
    name: '微博',
    icon: 'ri:weibo-fill',
    link: '#'
  },
  {
    name: 'Facebook',
    icon: 'ri:facebook-fill',
    link: '#'
  },
  {
    name: 'Instagram',
    icon: 'ri:instagram-fill',
    link: '#'
  }
]

// 联系表单
const contactForm = ref({
  name: '',
  email: '',
  phone: '',
  subject: '',
  message: ''
})

// 提交表单
const submitContactForm = () => {
  // 这里添加表单验证和提交逻辑
  alert('感谢您的留言，我们会尽快回复！')
  // 重置表单
  contactForm.value = {
    name: '',
    email: '',
    phone: '',
    subject: '',
    message: ''
  }
}
</script>

<template>
  <div class="contact-page">
    <Topbar />
    <SearchBar />
    
    <div class="contact-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:chat-bubble-left-right" />
          联系我们
        </h1>
        <p class="page-subtitle">随时为您提供帮助</p>
      </div>

      <!-- 联系信息 -->
      <section class="contact-info-section">
        <div class="info-grid">
          <div 
            v-for="(info, key) in contactInfo" 
            :key="key"
            class="info-card"
          >
            <div class="info-icon">
              <Icon :name="info.icon" />
            </div>
            <h3 class="info-title">{{ info.title }}</h3>
            <p class="info-content">{{ info.content }}</p>
          </div>
        </div>
      </section>

      <!-- 社交媒体 -->
      <section class="social-section">
        <h2 class="section-title">关注我们</h2>
        <div class="social-grid">
          <a 
            v-for="social in socialLinks" 
            :key="social.name"
            :href="social.link"
            class="social-link"
            target="_blank"
            rel="noopener noreferrer"
          >
            <Icon :name="social.icon" />
            <span>{{ social.name }}</span>
          </a>
        </div>
      </section>

      <!-- 联系表单 -->
      <section class="contact-form-section">
        <h2 class="section-title">发送消息</h2>
        <div class="form-container">
          <form class="contact-form" @submit.prevent="submitContactForm">
            <div class="form-row">
              <div class="form-group">
                <label>姓名</label>
                <input 
                  v-model="contactForm.name"
                  type="text"
                  placeholder="请输入您的姓名"
                  required
                >
              </div>
              <div class="form-group">
                <label>邮箱</label>
                <input 
                  v-model="contactForm.email"
                  type="email"
                  placeholder="请输入您的邮箱"
                  required
                >
              </div>
            </div>
            <div class="form-row">
              <div class="form-group">
                <label>电话</label>
                <input 
                  v-model="contactForm.phone"
                  type="tel"
                  placeholder="请输入您的电话"
                  required
                >
              </div>
              <div class="form-group">
                <label>主题</label>
                <input 
                  v-model="contactForm.subject"
                  type="text"
                  placeholder="请输入消息主题"
                  required
                >
              </div>
            </div>
            <div class="form-group">
              <label>消息内容</label>
              <textarea 
                v-model="contactForm.message"
                placeholder="请输入您的消息内容"
                rows="6"
                required
              />
            </div>
            <button type="submit" class="submit-btn">发送消息</button>
          </form>
        </div>
      </section>

      <!-- 地图 -->
      <section class="map-section">
        <h2 class="section-title">位置地图</h2>
        <div class="map-container">
          <!-- 这里可以嵌入地图组件 -->
          <div class="map-placeholder">
            <Icon name="heroicons:map" />
            <p>地图加载中...</p>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<style scoped>
.contact-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.contact-container {
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

/* 联系信息 */
.contact-info-section {
  margin-bottom: 60px;
}

.info-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 30px;
}

.info-card {
  background: white;
  border-radius: 16px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.info-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.info-icon {
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

.info-title {
  font-size: 1.2rem;
  color: #333;
  margin-bottom: 12px;
}

.info-content {
  color: #666;
  line-height: 1.6;
}

/* 社交媒体 */
.social-section {
  margin-bottom: 60px;
  text-align: center;
}

.section-title {
  font-size: 2rem;
  color: #333;
  margin-bottom: 40px;
}

.social-grid {
  display: flex;
  justify-content: center;
  gap: 20px;
  flex-wrap: wrap;
}

.social-link {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  color: #666;
  text-decoration: none;
  transition: all 0.3s ease;
}

.social-link:hover {
  color: var(--primary-color);
  transform: translateY(-3px);
}

.social-link .icon {
  font-size: 2rem;
}

.social-link span {
  font-size: 0.9rem;
}

/* 联系表单 */
.contact-form-section {
  margin-bottom: 60px;
}

.form-container {
  background: white;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.contact-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.form-row {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
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

/* 地图 */
.map-section {
  margin-bottom: 60px;
}

.map-container {
  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  aspect-ratio: 16/9;
}

.map-placeholder {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
  color: #666;
  background: #f8f9fa;
}

.map-placeholder .icon {
  font-size: 3rem;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .contact-container {
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

  .info-grid {
    grid-template-columns: 1fr;
  }

  .social-grid {
    gap: 16px;
  }

  .social-link .icon {
    font-size: 1.8rem;
  }

  .form-container {
    padding: 20px;
  }

  .form-row {
    grid-template-columns: 1fr;
  }

  .submit-btn {
    width: 100%;
  }
}
</style>
