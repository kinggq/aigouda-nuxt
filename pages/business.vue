<script setup lang="ts">
import { ref } from 'vue'

// 广告位类型
interface AdSpace {
  id: number
  name: string
  description: string
  price: number
  features: string[]
  icon: string
}

// 广告位数据
const adSpaces = ref<AdSpace[]>([
  {
    id: 1,
    name: '首页轮播图',
    description: '首页顶部轮播图广告位，展示效果最佳',
    price: 299,
    features: [
      '轮播展示，自动切换',
      '支持图片和视频',
      '点击跳转链接',
      '展示时间7天'
    ],
    icon: 'heroicons:photo'
  },
  {
    id: 2,
    name: '信息流广告',
    description: '首页信息流中的原生广告，自然融入内容',
    price: 199,
    features: [
      '原生展示，不打扰用户',
      '支持图文组合',
      '智能投放',
      '展示时间7天'
    ],
    icon: 'heroicons:newspaper'
  },
  {
    id: 3,
    name: '侧边栏广告',
    description: '页面侧边固定广告位，持续曝光',
    price: 149,
    features: [
      '固定位置展示',
      '支持图片和文字',
      '持续曝光',
      '展示时间7天'
    ],
    icon: 'heroicons:rectangle-stack'
  }
])

// 合作方式
const cooperationTypes = [
  {
    title: '广告投放',
    description: '在平台投放广告，精准触达目标用户',
    icon: 'heroicons:megaphone'
  },
  {
    title: '内容合作',
    description: '提供优质内容，提升品牌影响力',
    icon: 'heroicons:document-text'
  },
  {
    title: '活动合作',
    description: '联合举办活动，扩大品牌声量',
    icon: 'heroicons:presentation-chart-line'
  },
  {
    title: '商务合作',
    description: '深度商务合作，实现互利共赢',
    icon: 'heroicons:handshake'
  }
]

// 联系表单
const contactForm = ref({
  name: '',
  email: '',
  phone: '',
  company: '',
  message: ''
})

// 提交表单
const submitForm = () => {
  // 这里添加表单验证和提交逻辑
  alert('感谢您的咨询，我们会尽快与您联系！')
  // 重置表单
  contactForm.value = {
    name: '',
    email: '',
    phone: '',
    company: '',
    message: ''
  }
}
</script>

<template>
  <div class="business-page">
    <Topbar />
    <SearchBar />
    
    <div class="business-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:building-office" />
          商业合作
        </h1>
        <p class="page-subtitle">携手共创价值，实现互利共赢</p>
      </div>

      <!-- 合作方式 -->
      <section class="cooperation-section">
        <h2 class="section-title">合作方式</h2>
        <div class="cooperation-grid">
          <div 
            v-for="type in cooperationTypes" 
            :key="type.title"
            class="cooperation-card"
          >
            <div class="card-icon">
              <Icon :name="type.icon" />
            </div>
            <h3 class="card-title">{{ type.title }}</h3>
            <p class="card-description">{{ type.description }}</p>
          </div>
        </div>
      </section>

      <!-- 广告位介绍 -->
      <section class="ad-spaces-section">
        <h2 class="section-title">广告位介绍</h2>
        <div class="ad-spaces-grid">
          <div 
            v-for="space in adSpaces" 
            :key="space.id"
            class="ad-space-card"
          >
            <div class="card-header">
              <div class="card-icon">
                <Icon :name="space.icon" />
              </div>
              <h3 class="card-title">{{ space.name }}</h3>
            </div>
            <p class="card-description">{{ space.description }}</p>
            <ul class="feature-list">
              <li 
                v-for="feature in space.features" 
                :key="feature"
                class="feature-item"
              >
                <Icon name="heroicons:check-circle" />
                {{ feature }}
              </li>
            </ul>
            <div class="card-footer">
              <div class="price">
                <span class="currency">€</span>
                <span class="amount">{{ space.price }}</span>
                <span class="period">/周</span>
              </div>
              <button class="contact-btn">立即咨询</button>
            </div>
          </div>
        </div>
      </section>

      <!-- 联系我们 -->
      <section class="contact-section">
        <h2 class="section-title">联系我们</h2>
        <div class="contact-content">
          <div class="contact-info">
            <div class="info-item">
              <Icon name="heroicons:envelope" />
              <div class="info-content">
                <h3>商务合作</h3>
                <p>business@aigouda.com</p>
              </div>
            </div>
            <div class="info-item">
              <Icon name="heroicons:phone" />
              <div class="info-content">
                <h3>联系电话</h3>
                <p>+353 123 456 789</p>
              </div>
            </div>
            <div class="info-item">
              <Icon name="heroicons:map-pin" />
              <div class="info-content">
                <h3>公司地址</h3>
                <p>Dublin, Ireland</p>
              </div>
            </div>
          </div>
          <form class="contact-form" @submit.prevent="submitForm">
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
              <label>公司</label>
              <input 
                v-model="contactForm.company"
                type="text"
                placeholder="请输入您的公司名称"
                required
              >
            </div>
            <div class="form-group">
              <label>留言</label>
              <textarea 
                v-model="contactForm.message"
                placeholder="请输入您的留言内容"
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
.business-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.business-container {
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

/* 合作方式 */
.cooperation-section {
  margin-bottom: 60px;
}

.section-title {
  font-size: 2rem;
  color: #333;
  margin-bottom: 40px;
  text-align: center;
}

.cooperation-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 30px;
}

.cooperation-card {
  background: white;
  border-radius: 16px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.cooperation-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.card-icon {
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

.card-title {
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 12px;
}

.card-description {
  color: #666;
  line-height: 1.6;
}

/* 广告位介绍 */
.ad-spaces-section {
  margin-bottom: 60px;
}

.ad-spaces-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 30px;
}

.ad-space-card {
  background: white;
  border-radius: 16px;
  padding: 30px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.ad-space-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.card-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}

.card-icon {
  width: 48px;
  height: 48px;
  background: var(--primary-color);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 1.4rem;
}

.feature-list {
  margin: 20px 0;
  padding: 0;
  list-style: none;
}

.feature-item {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  margin-bottom: 12px;
}

.feature-item .icon {
  color: var(--primary-color);
}

.card-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 20px;
  padding-top: 20px;
  border-top: 1px solid #eee;
}

.price {
  display: flex;
  align-items: baseline;
}

.currency {
  font-size: 1.2rem;
  color: #666;
}

.amount {
  font-size: 2rem;
  font-weight: 600;
  color: #333;
  margin: 0 4px;
}

.period {
  color: #666;
  font-size: 0.9rem;
}

.contact-btn {
  padding: 8px 20px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.contact-btn:hover {
  opacity: 0.9;
}

/* 联系我们 */
.contact-section {
  margin-bottom: 60px;
}

.contact-content {
  display: grid;
  grid-template-columns: 1fr 2fr;
  gap: 40px;
  background: white;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.contact-info {
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
  margin-bottom: 4px;
}

.info-content p {
  color: #666;
}

.contact-form {
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
  .business-container {
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

  .cooperation-grid,
  .ad-spaces-grid {
    grid-template-columns: 1fr;
  }

  .contact-content {
    grid-template-columns: 1fr;
    gap: 30px;
    padding: 20px;
  }

  .contact-info {
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
