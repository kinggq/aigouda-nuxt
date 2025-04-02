<script setup lang="ts">
// 捎带类型
const carryTypes = [
  { id: 'find', name: '寻找捎带', icon: 'heroicons:user-group' },
  { id: 'offer', name: '提供捎带', icon: 'heroicons:hand-raised' }
]

// 物品类型
const itemTypes = [
  { id: 'documents', name: '文件', icon: 'heroicons:document' },
  { id: 'clothes', name: '衣物', icon: 'heroicons:shirt' },
  { id: 'food', name: '食品', icon: 'heroicons:cake' },
  { id: 'electronics', name: '电子产品', icon: 'heroicons:device-phone-mobile' },
  { id: 'other', name: '其他', icon: 'heroicons:cube' }
]

// 状态
const loading = ref(false)
const currentType = ref('find')
const selectedItemType = ref('')
const showPublishModal = ref(false)
const currentPage = ref(1)
const hasMore = ref(true)

// 表单数据
const formData = ref({
  type: 'find',
  itemType: '',
  title: '',
  description: '',
  weight: '',
  price: '',
  from: '',
  to: '',
  contact: {
    wechat: '',
    phone: '',
    email: ''
  },
  images: [] as string[]
})

// 模拟数据
const carryList = ref([
  {
    id: 1,
    type: 'find',
    itemType: 'documents',
    title: '寻找从都柏林到北京的捎带',
    description: '需要捎带一份重要文件，约0.5kg，希望尽快送达',
    weight: '0.5',
    price: '20',
    from: '都柏林',
    to: '北京',
    contact: {
      wechat: 'wxid_123456',
      phone: '+353 123 456 789',
      email: 'example@email.com'
    },
    images: ['#f0f0f0', '#e0e0e0'],
    createdAt: '2024-03-15',
    status: 'active'
  },
  {
    id: 2,
    type: 'offer',
    itemType: 'clothes',
    title: '3月20日都柏林到上海，可捎带',
    description: '回国探亲，可捎带衣物等物品，限重5kg',
    weight: '5',
    price: '15',
    from: '都柏林',
    to: '上海',
    contact: {
      wechat: 'wxid_789012',
      phone: '+353 987 654 321',
      email: 'test@email.com'
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

// 发布新捎带
const publishCarry = () => {
  // 这里添加发布逻辑
  showPublishModal.value = false
  formData.value = {
    type: 'find',
    itemType: '',
    title: '',
    description: '',
    weight: '',
    price: '',
    from: '',
    to: '',
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
  <div class="carry-page">
    <Topbar />
    <SearchBar />
    
    <div class="carry-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:airplane" />
          捎带服务
        </h1>
        <p class="page-subtitle">寻找可靠的捎带服务，让物品安全送达</p>
      </div>

      <!-- 类型选择 -->
      <div class="type-selector">
        <div 
          v-for="type in carryTypes" 
          :key="type.id"
          class="type-item"
          :class="{ active: currentType === type.id }"
          @click="currentType = type.id"
        >
          <Icon :name="type.icon" />
          {{ type.name }}
        </div>
      </div>

      <!-- 发布按钮 -->
      <button class="publish-btn" @click="showPublishModal = true">
        <Icon name="heroicons:plus" />
        发布捎带
      </button>

      <!-- 列表内容 -->
      <div class="carry-list">
        <div 
          v-for="item in carryList" 
          v-show="item.type === currentType"
          :key="item.id"
          class="carry-item"
        >
          <div class="item-header">
            <div class="item-type">
              <Icon :name="itemTypes.find(t => t.id === item.itemType)?.icon" />
              {{ itemTypes.find(t => t.id === item.itemType)?.name }}
            </div>
            <div class="item-status" :class="item.status">
              {{ item.status === 'active' ? '进行中' : '已完成' }}
            </div>
          </div>
          
          <h3 class="item-title">{{ item.title }}</h3>
          <p class="item-description">{{ item.description }}</p>
          
          <div class="item-info">
            <div class="info-item">
              <Icon name="heroicons:map-pin" />
              {{ item.from }} → {{ item.to }}
            </div>
            <div class="info-item">
              <Icon name="heroicons:scale" />
              {{ item.weight }}kg
            </div>
            <div class="info-item">
              <Icon name="heroicons:currency-euro" />
              €{{ item.price }}/kg
            </div>
          </div>
          
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
          <h2>发布捎带</h2>
          <button class="close-btn" @click="showPublishModal = false">
            <Icon name="heroicons:x-mark" />
          </button>
        </div>
        
        <div class="modal-body">
          <div class="form-group">
            <label>捎带类型</label>
            <div class="type-selector">
              <div 
                v-for="type in carryTypes" 
                :key="type.id"
                class="type-item"
                :class="{ active: formData.type === type.id }"
                @click="formData.type = type.id"
              >
                <Icon :name="type.icon" />
                {{ type.name }}
              </div>
            </div>
          </div>
          
          <div class="form-group">
            <label>物品类型</label>
            <div class="item-type-selector">
              <div 
                v-for="type in itemTypes" 
                :key="type.id"
                class="type-item"
                :class="{ active: formData.itemType === type.id }"
                @click="formData.itemType = type.id"
              >
                <Icon :name="type.icon" />
                {{ type.name }}
              </div>
            </div>
          </div>
          
          <div class="form-group">
            <label>标题</label>
            <input 
              v-model="formData.title"
              type="text"
              placeholder="请输入标题"
              class="form-input"
            >
          </div>
          
          <div class="form-group">
            <label>描述</label>
            <textarea 
              v-model="formData.description"
              placeholder="请详细描述您的需求"
              class="form-textarea"
            />
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label>重量(kg)</label>
              <input 
                v-model="formData.weight"
                type="number"
                placeholder="请输入重量"
                class="form-input"
              >
            </div>
            
            <div class="form-group">
              <label>价格(€/kg)</label>
              <input 
                v-model="formData.price"
                type="number"
                placeholder="请输入价格"
                class="form-input"
              >
            </div>
          </div>
          
          <div class="form-row">
            <div class="form-group">
              <label>出发地</label>
              <input 
                v-model="formData.from"
                type="text"
                placeholder="请输入出发地"
                class="form-input"
              >
            </div>
            
            <div class="form-group">
              <label>目的地</label>
              <input 
                v-model="formData.to"
                type="text"
                placeholder="请输入目的地"
                class="form-input"
              >
            </div>
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
          <button class="submit-btn" @click="publishCarry">发布</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.carry-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.carry-container {
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
.carry-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.carry-item {
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

.item-description {
  color: #666;
  line-height: 1.6;
  margin-bottom: 20px;
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
  .carry-container {
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
