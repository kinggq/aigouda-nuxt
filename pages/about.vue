<script setup lang="ts">
import { ref } from 'vue'

// 公司信息
const companyInfo = {
  name: '爱购达',
  description: '爱购达是一个专注于为爱尔兰华人提供全方位生活服务的综合性平台。我们致力于打造一个便捷、高效、温暖的社区服务平台。',
  founded: '2024年',
  mission: '让每一位在爱尔兰的华人生活更简单、更美好',
  vision: '成为爱尔兰最受欢迎的华人生活服务平台'
}

// 核心价值
const coreValues = [
  {
    title: '用户至上',
    description: '始终以用户需求为中心，提供优质服务',
    icon: 'heroicons:user-group'
  },
  {
    title: '诚信为本',
    description: '坚持诚信经营，建立长期信任关系',
    icon: 'heroicons:shield-check'
  },
  {
    title: '创新驱动',
    description: '持续创新，不断优化用户体验',
    icon: 'heroicons:light-bulb'
  },
  {
    title: '社区共建',
    description: '与用户共同建设美好社区',
    icon: 'heroicons:heart'
  }
]

// 发展历程
const history = [
  {
    year: '2024',
    title: '平台成立',
    description: '爱购达正式上线，开始为爱尔兰华人提供服务'
  },
  {
    year: '2024 Q2',
    title: '功能完善',
    description: '推出租房、二手交易、论坛等核心功能'
  },
  {
    year: '2024 Q3',
    title: '社区建设',
    description: '建立活跃的用户社区，促进用户交流'
  },
  {
    year: '2024 Q4',
    title: '服务升级',
    description: '推出更多增值服务，提升用户体验'
  }
]

// 团队信息
const team = [
  {
    name: '张三',
    role: '创始人 & CEO',
    avatar: '/images/team/ceo.jpg',
    description: '拥有10年互联网行业经验，致力于为华人社区提供更好的服务'
  },
  {
    name: '李四',
    role: '技术总监',
    avatar: '/images/team/cto.jpg',
    description: '全栈开发专家，负责平台技术架构和开发'
  },
  {
    name: '王五',
    role: '运营总监',
    avatar: '/images/team/coo.jpg',
    description: '负责平台运营和用户增长，打造优质社区氛围'
  }
]
</script>

<template>
  <div class="about-page">
    <Topbar />
    <SearchBar />
    
    <div class="about-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:building-office" />
          关于我们
        </h1>
        <p class="page-subtitle">了解爱购达的故事</p>
      </div>

      <!-- 公司介绍 -->
      <section class="company-section">
        <div class="company-content">
          <div class="company-text">
            <h2 class="section-title">{{ companyInfo.name }}</h2>
            <p class="company-description">{{ companyInfo.description }}</p>
            <div class="company-mission">
              <h3>我们的使命</h3>
              <p>{{ companyInfo.mission }}</p>
            </div>
            <div class="company-vision">
              <h3>我们的愿景</h3>
              <p>{{ companyInfo.vision }}</p>
            </div>
          </div>
          <div class="company-image">
            <!-- <img src="/images/about/company.jpg" alt="公司照片"> -->
          </div>
        </div>
      </section>

      <!-- 核心价值 -->
      <section class="values-section">
        <h2 class="section-title">核心价值</h2>
        <div class="values-grid">
          <div 
            v-for="value in coreValues" 
            :key="value.title"
            class="value-card"
          >
            <div class="value-icon">
              <Icon :name="value.icon" />
            </div>
            <h3 class="value-title">{{ value.title }}</h3>
            <p class="value-description">{{ value.description }}</p>
          </div>
        </div>
      </section>

      <!-- 发展历程 -->
      <section class="history-section">
        <h2 class="section-title">发展历程</h2>
        <div class="history-timeline">
          <div 
            v-for="(item, index) in history" 
            :key="item.year"
            class="history-item"
            :class="{ 'history-item-right': index % 2 === 1 }"
          >
            <div class="history-year">{{ item.year }}</div>
            <div class="history-content">
              <h3>{{ item.title }}</h3>
              <p>{{ item.description }}</p>
            </div>
            <div class="history-dot"/>
          </div>
        </div>
      </section>

      <!-- 团队介绍 -->
      <section class="team-section">
        <h2 class="section-title">我们的团队</h2>
        <div class="team-grid">
          <div 
            v-for="member in team" 
            :key="member.name"
            class="team-card"
          >
            <div class="team-avatar">
              <!-- <img :src="member.avatar" :alt="member.name"> -->
            </div>
            <div class="team-info">
              <h3 class="team-name">{{ member.name }}</h3>
              <p class="team-role">{{ member.role }}</p>
              <p class="team-description">{{ member.description }}</p>
            </div>
          </div>
        </div>
      </section>
    </div>
  </div>
</template>

<style scoped>
.about-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.about-container {
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

/* 公司介绍 */
.company-section {
  margin-bottom: 60px;
}

.company-content {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  align-items: center;
  background: white;
  border-radius: 16px;
  padding: 40px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.company-text {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.section-title {
  font-size: 2rem;
  color: #333;
  margin-bottom: 16px;
}

.company-description {
  font-size: 1.1rem;
  line-height: 1.8;
  color: #666;
}

.company-mission,
.company-vision {
  padding: 20px;
  background: #f8f9fa;
  border-radius: 12px;
}

.company-mission h3,
.company-vision h3 {
  font-size: 1.2rem;
  color: #333;
  margin-bottom: 8px;
}

.company-mission p,
.company-vision p {
  color: #666;
  line-height: 1.6;
}

.company-image img {
  width: 100%;
  height: auto;
  border-radius: 12px;
}

/* 核心价值 */
.values-section {
  margin-bottom: 60px;
}

.values-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 30px;
}

.value-card {
  background: white;
  border-radius: 16px;
  padding: 30px;
  text-align: center;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.value-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.value-icon {
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

.value-title {
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 12px;
}

.value-description {
  color: #666;
  line-height: 1.6;
}

/* 发展历程 */
.history-section {
  margin-bottom: 60px;
}

.history-timeline {
  position: relative;
  max-width: 800px;
  margin: 0 auto;
  padding: 40px 0;
}

.history-timeline::before {
  content: '';
  position: absolute;
  top: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 2px;
  height: 100%;
  background: var(--primary-color);
}

.history-item {
  position: relative;
  margin-bottom: 40px;
  width: 50%;
  padding-right: 40px;
}

.history-item-right {
  margin-left: 50%;
  padding-right: 0;
  padding-left: 40px;
}

.history-year {
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--primary-color);
  margin-bottom: 8px;
}

.history-content {
  background: white;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.history-content h3 {
  font-size: 1.2rem;
  color: #333;
  margin-bottom: 8px;
}

.history-content p {
  color: #666;
  line-height: 1.6;
}

.history-dot {
  position: absolute;
  top: 0;
  right: -6px;
  width: 12px;
  height: 12px;
  background: var(--primary-color);
  border-radius: 50%;
}

.history-item-right .history-dot {
  right: auto;
  left: -6px;
}

/* 团队介绍 */
.team-section {
  margin-bottom: 60px;
}

.team-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 30px;
}

.team-card {
  background: white;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: all 0.3s ease;
}

.team-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.team-avatar {
  width: 100%;
  height: 200px;
  overflow: hidden;
}

.team-avatar img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.team-info {
  padding: 20px;
}

.team-name {
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 4px;
}

.team-role {
  font-size: 1rem;
  color: var(--primary-color);
  margin-bottom: 12px;
}

.team-description {
  color: #666;
  line-height: 1.6;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .about-container {
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

  .company-content {
    grid-template-columns: 1fr;
    gap: 30px;
    padding: 20px;
  }

  .company-description {
    font-size: 1rem;
  }

  .values-grid,
  .team-grid {
    grid-template-columns: 1fr;
  }

  .history-timeline::before {
    left: 30px;
  }

  .history-item {
    width: 100%;
    margin-left: 0;
    padding-left: 60px;
    padding-right: 0;
  }

  .history-item-right {
    margin-left: 0;
    padding-left: 60px;
  }

  .history-dot {
    left: 24px;
  }

  .history-item-right .history-dot {
    left: 24px;
  }
}
</style>
