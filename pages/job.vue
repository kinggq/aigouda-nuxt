<script setup lang="ts">
// 职位类型
const jobTypes = [
  { id: 'recruit', name: '招聘', icon: 'heroicons:briefcase' },
  { id: 'seek', name: '求职', icon: 'heroicons:user' }
]

// 工作类型
const workTypes = [
  { id: 'full-time', name: '全职', icon: 'heroicons:clock' },
  { id: 'part-time', name: '兼职', icon: 'heroicons:calendar' },
  { id: 'internship', name: '实习', icon: 'heroicons:academic-cap' },
  { id: 'freelance', name: '自由职业', icon: 'heroicons:computer-desktop' }
]

// 工作经验
const experienceLevels = [
  { id: 'entry', name: '应届生', icon: 'heroicons:academic-cap' },
  { id: 'junior', name: '初级(1-3年)', icon: 'heroicons:star' },
  { id: 'middle', name: '中级(3-5年)', icon: 'heroicons:star' },
  { id: 'senior', name: '高级(5年以上)', icon: 'heroicons:star' },
  { id: 'expert', name: '专家(8年以上)', icon: 'heroicons:star' }
]

// 状态
const loading = ref(false)
const currentType = ref('recruit')
const showPublishModal = ref(false)
const currentPage = ref(1)
const hasMore = ref(true)

// 筛选条件
const filters = ref({
  workType: '',
  experience: '',
  salary: '',
  location: ''
})

// 表单数据
const formData = ref({
  type: 'recruit',
  title: '',
  company: '',
  workType: '',
  experience: '',
  salary: '',
  location: '',
  description: '',
  requirements: '',
  contact: {
    wechat: '',
    phone: '',
    email: ''
  },
  images: [] as string[]
})

// 模拟数据
const jobList = ref([
  {
    id: 1,
    type: 'recruit',
    title: '高级前端开发工程师',
    company: 'Tech Solutions Ltd',
    workType: 'full-time',
    experience: 'senior',
    salary: '€50,000 - €70,000',
    location: '都柏林',
    description: '负责公司核心产品的前端开发工作，使用Vue.js、TypeScript等技术栈。',
    requirements: '5年以上前端开发经验，精通Vue.js、TypeScript，有大型项目经验。',
    contact: {
      wechat: 'wxid_123456',
      phone: '+353 123 456 789',
      email: 'hr@techsolutions.com'
    },
    images: ['#f0f0f0', '#e0e0e0'],
    createdAt: '2024-03-15',
    status: 'active'
  },
  {
    id: 2,
    type: 'seek',
    title: '全栈开发工程师求职',
    workType: 'full-time',
    experience: 'middle',
    salary: '€40,000 - €60,000',
    location: '都柏林/远程',
    description: '3年全栈开发经验，熟悉前后端技术栈，有良好的团队协作能力。',
    requirements: '熟悉Vue.js、Node.js、MySQL等技术，有项目经验。',
    contact: {
      wechat: 'wxid_789012',
      phone: '+353 987 654 321',
      email: 'jobseeker@email.com'
    },
    images: ['#f5f5f5', '#e5e5e5'],
    createdAt: '2024-03-14',
    status: 'active'
  }
])

// 复制联系方式
const copyContact = (type: string, value: string) => {
  navigator.clipboard.writeText(value)
  // 这里可以添加复制成功的提示
}

// 发布新职位
const publishJob = () => {
  // 这里添加发布逻辑
  showPublishModal.value = false
  formData.value = {
    type: 'recruit',
    title: '',
    company: '',
    workType: '',
    experience: '',
    salary: '',
    location: '',
    description: '',
    requirements: '',
    contact: {
      wechat: '',
      phone: '',
      email: ''
    },
    images: []
  }
}

// 加载更多
const loadMore = async () => {
  if (loading.value || !hasMore.value) return
  
  loading.value = true
  // 模拟加载延迟
  await new Promise(resolve => setTimeout(resolve, 1000))
  
  // 这里添加加载更多数据的逻辑
  currentPage.value++
  if (currentPage.value >= 3) {
    hasMore.value = false
  }
  
  loading.value = false
}

// 监听滚动
onMounted(() => {
  window.addEventListener('scroll', () => {
    const scrollHeight = document.documentElement.scrollHeight
    const scrollTop = document.documentElement.scrollTop
    const clientHeight = document.documentElement.clientHeight
    
    if (scrollHeight - scrollTop - clientHeight < 100) {
      loadMore()
    }
  })
})
</script>

<template>
  <div class="job-page">
    <Topbar />
    <SearchBar />
    
    <div class="job-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:briefcase" />
          招聘求职
        </h1>
        <p class="page-subtitle">寻找理想的工作机会，或发布招聘信息</p>
      </div>

      <!-- 类型选择 -->
      <div class="type-selector">
        <div 
          v-for="type in jobTypes" 
          :key="type.id"
          class="type-item"
          :class="{ active: currentType === type.id }"
          @click="currentType = type.id"
        >
          <Icon :name="type.icon" />
          {{ type.name }}
        </div>
      </div>

      <!-- 筛选条件 -->
      <div class="filter-section">
        <div class="filter-group">
          <label>工作类型</label>
          <div class="filter-options">
            <div 
              v-for="type in workTypes" 
              :key="type.id"
              class="filter-option"
              :class="{ active: filters.workType === type.id }"
              @click="filters.workType = type.id"
            >
              <Icon :name="type.icon" />
              {{ type.name }}
            </div>
          </div>
        </div>

        <div class="filter-group">
          <label>工作经验</label>
          <div class="filter-options">
            <div 
              v-for="level in experienceLevels" 
              :key="level.id"
              class="filter-option"
              :class="{ active: filters.experience === level.id }"
              @click="filters.experience = level.id"
            >
              <Icon :name="level.icon" />
              {{ level.name }}
            </div>
          </div>
        </div>

        <div class="filter-row">
          <div class="filter-group">
            <label>薪资范围</label>
            <input 
              v-model="filters.salary"
              type="text"
              placeholder="输入薪资范围"
              class="filter-input"
            >
          </div>

          <div class="filter-group">
            <label>工作地点</label>
            <input 
              v-model="filters.location"
              type="text"
              placeholder="输入工作地点"
              class="filter-input"
            >
          </div>
        </div>
      </div>

      <!-- 发布按钮 -->
      <button class="publish-btn" @click="showPublishModal = true">
        <Icon name="heroicons:plus" />
        发布{{ currentType === 'recruit' ? '招聘' : '求职' }}
      </button>

      <!-- 列表内容 -->
      <div class="job-list">
        <div 
          v-for="item in jobList" 
          v-show="item.type === currentType"
          :key="item.id"
          class="job-item"
        >
          <div class="item-header">
            <div class="item-type">
              <Icon :name="workTypes.find(t => t.id === item.workType)?.icon" />
              {{ workTypes.find(t => t.id === item.workType)?.name }}
            </div>
            <div class="item-status" :class="item.status">
              {{ item.status === 'active' ? '招聘中' : '已结束' }}
            </div>
          </div>
          
          <h3 class="item-title">{{ item.title }}</h3>
          <div v-if="item.type === 'recruit'" class="item-company">
            <Icon name="heroicons:building-office" />
            {{ item.company }}
          </div>
          
          <div class="item-info">
            <div class="info-item">
              <Icon name="heroicons:map-pin" />
              {{ item.location }}
            </div>
            <div class="info-item">
              <Icon :name="experienceLevels.find(l => l.id === item.experience)?.icon" />
              {{ experienceLevels.find(l => l.id === item.experience)?.name }}
            </div>
            <div class="info-item">
              <Icon name="heroicons:currency-euro" />
              {{ item.salary }}
            </div>
          </div>
          
          <p class="item-description">{{ item.description }}</p>
          <p class="item-requirements">{{ item.requirements }}</p>
          
          <div class="item-images">
            <div 
              v-for="(image, index) in item.images" 
              :key="index"
              class="image-preview"
              :style="{ backgroundColor: image }"
            >
              <Icon name="heroicons:photo" />
            </div>
          </div>
          
          <div class="item-contact">
            <div class="contact-title">联系方式</div>
            <div class="contact-list">
              <div class="contact-item" @click="copyContact('wechat', item.contact.wechat)">
                <Icon name="ri:wechat-fill" />
                <span>微信：{{ item.contact.wechat }}</span>
              </div>
              <div class="contact-item" @click="copyContact('phone', item.contact.phone)">
                <Icon name="heroicons:phone" />
                <span>电话：{{ item.contact.phone }}</span>
              </div>
              <div class="contact-item" @click="copyContact('email', item.contact.email)">
                <Icon name="heroicons:envelope" />
                <span>邮箱：{{ item.contact.email }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- 加载状态 -->
      <div v-if="loading" class="loading-state">
        <Icon name="heroicons:arrow-path" class="animate-spin" />
        加载中...
      </div>
      <div v-if="!hasMore && !loading" class="no-more">
        没有更多内容了
      </div>
    </div>

    <!-- 发布弹窗 -->
    <div v-if="showPublishModal" class="modal-overlay">
      <div class="modal-content">
        <div class="modal-header">
          <h2>发布{{ currentType === 'recruit' ? '招聘' : '求职' }}</h2>
          <button class="close-btn" @click="showPublishModal = false">
            <Icon name="heroicons:x-mark" />
          </button>
        </div>
        
        <div class="modal-body">
          <div class="form-group">
            <label>标题</label>
            <input 
              v-model="formData.title"
              type="text"
              :placeholder="currentType === 'recruit' ? '请输入职位名称' : '请输入求职意向'"
              class="form-input"
            >
          </div>
          
          <div v-if="currentType === 'recruit'" class="form-group">
            <label>公司名称</label>
            <input 
              v-model="formData.company"
              type="text"
              placeholder="请输入公司名称"
              class="form-input"
            >
          </div>
          
          <div class="form-group">
            <label>工作类型</label>
            <div class="work-type-selector">
              <div 
                v-for="type in workTypes" 
                :key="type.id"
                class="type-item"
                :class="{ active: formData.workType === type.id }"
                @click="formData.workType = type.id"
              >
                <Icon :name="type.icon" />
                {{ type.name }}
              </div>
            </div>
          </div>
          
          <div class="form-group">
            <label>工作经验</label>
            <div class="experience-selector">
              <div 
                v-for="level in experienceLevels" 
                :key="level.id"
                class="type-item"
                :class="{ active: formData.experience === level.id }"
                @click="formData.experience = level.id"
              >
                <Icon :name="level.icon" />
                {{ level.name }}
              </div>
            </div>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label>薪资范围</label>
              <input 
                v-model="formData.salary"
                type="text"
                placeholder="请输入薪资范围"
                class="form-input"
              >
            </div>
            
            <div class="form-group">
              <label>工作地点</label>
              <input 
                v-model="formData.location"
                type="text"
                placeholder="请输入工作地点"
                class="form-input"
              >
            </div>
          </div>
          
          <div class="form-group">
            <label>{{ currentType === 'recruit' ? '职位描述' : '个人简介' }}</label>
            <textarea 
              v-model="formData.description"
              :placeholder="currentType === 'recruit' ? '请详细描述职位信息' : '请介绍您的个人情况'"
              class="form-textarea"
            />
          </div>
          
          <div class="form-group">
            <label>{{ currentType === 'recruit' ? '任职要求' : '技能特长' }}</label>
            <textarea 
              v-model="formData.requirements"
              :placeholder="currentType === 'recruit' ? '请详细描述任职要求' : '请描述您的技能特长'"
              class="form-textarea"
            />
          </div>
          
          <div class="form-group">
            <label>联系方式</label>
            <div class="contact-inputs">
              <input 
                v-model="formData.contact.wechat"
                type="text"
                placeholder="微信号"
                class="form-input"
              >
              <input 
                v-model="formData.contact.phone"
                type="tel"
                placeholder="电话号码"
                class="form-input"
              >
              <input 
                v-model="formData.contact.email"
                type="email"
                placeholder="电子邮箱"
                class="form-input"
              >
            </div>
          </div>
          
          <div class="form-group">
            <label>图片上传</label>
            <div class="image-upload">
              <div 
                v-for="(image, index) in formData.images" 
                :key="index"
                class="image-preview"
                :style="{ backgroundColor: image }"
              >
                <button class="remove-image" @click="formData.images.splice(index, 1)">
                  <Icon name="heroicons:x-mark" />
                </button>
              </div>
              <button v-if="formData.images.length < 3" class="upload-btn">
                <Icon name="heroicons:photo" />
                上传图片
              </button>
            </div>
          </div>
        </div>
        
        <div class="modal-footer">
          <button class="cancel-btn" @click="showPublishModal = false">取消</button>
          <button class="submit-btn" @click="publishJob">发布</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.job-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.job-container {
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

/* 类型选择器 */
.type-selector {
  display: flex;
  gap: 20px;
  margin-bottom: 30px;
}

.type-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 24px;
  border-radius: 8px;
  background: white;
  color: #666;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.type-item:hover {
  color: var(--primary-color);
  transform: translateY(-2px);
}

.type-item.active {
  background: var(--primary-color);
  color: white;
}

/* 筛选区域 */
.filter-section {
  background: white;
  border-radius: 16px;
  padding: 24px;
  margin-bottom: 30px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.filter-group {
  margin-bottom: 20px;
}

.filter-group label {
  display: block;
  margin-bottom: 12px;
  color: #333;
  font-weight: 500;
}

.filter-options {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
}

.filter-option {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border-radius: 6px;
  background: #f8f9fa;
  color: #666;
  cursor: pointer;
  transition: all 0.3s ease;
}

.filter-option:hover {
  background: #e9ecef;
  color: var(--primary-color);
}

.filter-option.active {
  background: var(--primary-color);
  color: white;
}

.filter-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.filter-input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.filter-input:focus {
  border-color: var(--primary-color);
  outline: none;
}

/* 发布按钮 */
.publish-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 24px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  margin-bottom: 30px;
}

.publish-btn:hover {
  opacity: 0.9;
  transform: translateY(-2px);
}

/* 列表内容 */
.job-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.job-item {
  background: white;
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.item-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.item-type {
  display: flex;
  align-items: center;
  gap: 8px;
  color: var(--primary-color);
  font-weight: 500;
}

.item-status {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.9rem;
}

.item-status.active {
  background: #e6f7ed;
  color: #2ecc71;
}

.item-status.completed {
  background: #f5f5f5;
  color: #666;
}

.item-title {
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 12px;
}

.item-company {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  margin-bottom: 16px;
}

.item-info {
  display: flex;
  gap: 20px;
  margin-bottom: 20px;
}

.info-item {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
}

.item-description,
.item-requirements {
  color: #666;
  line-height: 1.6;
  margin-bottom: 16px;
}

.item-images {
  display: flex;
  gap: 12px;
  margin-bottom: 20px;
}

.image-preview {
  width: 100px;
  height: 100px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 2rem;
}

.item-contact {
  border-top: 1px solid #eee;
  padding-top: 20px;
}

.contact-title {
  font-weight: 500;
  color: #333;
  margin-bottom: 12px;
}

.contact-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  cursor: pointer;
  padding: 8px;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.contact-item:hover {
  background: #f8f9fa;
  color: var(--primary-color);
}

/* 加载状态 */
.loading-state,
.no-more {
  text-align: center;
  padding: 20px;
  color: #666;
}

.loading-state .icon {
  font-size: 1.5rem;
  margin-right: 8px;
}

/* 弹窗样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 16px;
  width: 90%;
  max-width: 600px;
  max-height: 90vh;
  overflow-y: auto;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border-bottom: 1px solid #eee;
}

.modal-header h2 {
  font-size: 1.5rem;
  color: #333;
}

.close-btn {
  background: none;
  border: none;
  color: #666;
  cursor: pointer;
  padding: 8px;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.close-btn:hover {
  background: #f8f9fa;
  color: #333;
}

.modal-body {
  padding: 20px;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: #333;
  font-weight: 500;
}

.form-input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.form-input:focus {
  border-color: var(--primary-color);
  outline: none;
}

.form-textarea {
  width: 100%;
  height: 120px;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  resize: vertical;
  transition: all 0.3s ease;
}

.form-textarea:focus {
  border-color: var(--primary-color);
  outline: none;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 20px;
}

.contact-inputs {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}

.image-upload {
  display: flex;
  gap: 12px;
  flex-wrap: wrap;
}

.upload-btn {
  width: 100px;
  height: 100px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: #666;
  cursor: pointer;
  transition: all 0.3s ease;
}

.upload-btn:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.remove-image {
  position: absolute;
  top: 4px;
  right: 4px;
  background: rgba(0, 0, 0, 0.5);
  color: white;
  border: none;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.remove-image:hover {
  background: rgba(0, 0, 0, 0.7);
}

.modal-footer {
  padding: 20px;
  border-top: 1px solid #eee;
  display: flex;
  justify-content: flex-end;
  gap: 12px;
}

.cancel-btn,
.submit-btn {
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.cancel-btn {
  background: #f8f9fa;
  color: #666;
  border: none;
}

.submit-btn {
  background: var(--primary-color);
  color: white;
  border: none;
}

.cancel-btn:hover {
  background: #eee;
}

.submit-btn:hover {
  opacity: 0.9;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .job-container {
    padding: 10px;
  }

  .page-title {
    font-size: 2rem;
  }

  .page-subtitle {
    font-size: 1rem;
  }

  .type-selector {
    flex-direction: column;
  }

  .type-item {
    width: 100%;
    justify-content: center;
  }

  .filter-section {
    padding: 16px;
  }

  .filter-options {
    gap: 8px;
  }

  .filter-option {
    padding: 6px 12px;
    font-size: 0.9rem;
  }

  .filter-row {
    grid-template-columns: 1fr;
  }

  .publish-btn {
    width: 100%;
    justify-content: center;
  }

  .item-info {
    flex-direction: column;
    gap: 8px;
  }

  .contact-list {
    flex-direction: column;
  }

  .modal-content {
    width: 95%;
    margin: 10px;
  }

  .form-row {
    grid-template-columns: 1fr;
  }

  .image-upload {
    justify-content: center;
  }
}
</style>
