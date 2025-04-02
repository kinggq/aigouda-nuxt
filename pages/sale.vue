<script setup lang="ts">
import { useRoute } from 'vue-router'

const route = useRoute()
const category = ref(route.query.category || 'all')

const categories = [
  { id: 'all', label: '全部', icon: 'heroicons:square-2-stack' },
  { id: 'electronics', label: '电子产品', icon: 'heroicons:device-phone-mobile' },
  { id: 'daily', label: '生活用品', icon: 'heroicons:shopping-bag' },
  { id: 'furniture', label: '家具家电', icon: 'heroicons:home' },
  { id: 'other', label: '其他', icon: 'heroicons:ellipsis-horizontal' }
]
interface Item {
  id: number;
  title: string;
  price: string;
  category: string;
  condition: string;
  location: string;
  images: string[];
  description: string;
  contact: {
    wechat: string;
    phone: string;
  }
}
const items = ref<Item[]>([
  {
    id: 1,
    title: 'iPhone 13 Pro Max',
    price: '€800',
    category: 'electronics',
    condition: 'like_new',
    location: '都柏林2区',
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    description: '使用一年，电池健康度95%，无划痕，配件齐全',
    contact: {
      wechat: 'seller_1',
      phone: '+353 87 123 4567'
    }
  },
  {
    id: 2,
    title: '宜家双人沙发',
    price: '€200',
    category: 'furniture',
    condition: 'good',
    location: '都柏林4区',
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    description: '使用两年，轻微磨损，需自取',
    contact: {
      wechat: 'seller_2',
      phone: '+353 87 234 5678'
    }
  },
  {
    id: 3,
    title: '厨房用品套装',
    price: '€50',
    category: 'daily',
    condition: 'new',
    location: '都柏林1区',
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    description: '全新未拆封，包含锅具、餐具等',
    contact: {
      wechat: 'seller_3',
      phone: '+353 87 345 6789'
    }
  }
])

const filteredItems = computed(() => {
  if (category.value === 'all') return items.value
  return items.value.filter(item => item.category === category.value)
})

const showContactModal = ref(false)
const showNewItemModal = ref(false)
const selectedItem = ref<Item | null>(null)
const copySuccess = ref({
  wechat: false,
  phone: false
})
const isLoading = ref(true)
const newItem = ref({
  title: '',
  price: '',
  category: 'electronics',
  condition: 'new',
  location: '',
  images: [] as string[],
  description: '',
  contact: {
    wechat: '',
    phone: ''
  }
})

// 分页相关状态
const pageSize = 20
const currentPage = ref(1)
const isLoadingMore = ref(false)
const hasMore = ref(true)

// 模拟加载更多数据
const loadMoreItems = async () => {
  if (isLoadingMore.value || !hasMore.value) return
  
  isLoadingMore.value = true
  try {
    // 模拟API请求延迟
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // 模拟新数据
    const newItems = Array.from({ length: 5 }, (_, index) => ({
      id: items.value.length + index + 1,
      title: `新商品 ${items.value.length + index + 1}`,
      price: `€${Math.floor(Math.random() * 500 + 50)}`,
      category: categories[Math.floor(Math.random() * 4) + 1].id,
      condition: ['new', 'like_new', 'good', 'fair', 'poor'][Math.floor(Math.random() * 5)],
      location: '都柏林2区',
      images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
      description: '这是一个示例商品描述...',
      contact: {
        wechat: `seller_${items.value.length + index + 1}`,
        phone: `+353 87 ${Math.floor(Math.random() * 9000000 + 1000000)}`
      }
    }))
    
    items.value.push(...newItems)
    currentPage.value++
    
    // 模拟数据加载完毕
    if (currentPage.value >= 3) {
      hasMore.value = false
    }
  } catch (error) {
    console.error('加载更多数据失败:', error)
  } finally {
    isLoadingMore.value = false
  }
}

// 监听滚动事件
const handleScroll = () => {
  const scrollHeight = document.documentElement.scrollHeight
  const scrollTop = document.documentElement.scrollTop
  const clientHeight = document.documentElement.clientHeight
  
  // 当滚动到距离底部100px时触发加载
  if (scrollHeight - scrollTop - clientHeight < 100) {
    loadMoreItems()
  }
}

// 组件挂载时添加滚动监听
onMounted(() => {
  setTimeout(() => {
    isLoading.value = false
  }, 1500)
  window.addEventListener('scroll', handleScroll)
})

// 组件卸载时移除滚动监听
onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

// 空状态数据
const emptyStateData = {
  all: {
    title: '暂无商品',
    icon: 'heroicons:shopping-bag',
    description: '暂时没有可用的商品，请稍后再试'
  },
  electronics: {
    title: '暂无电子产品',
    icon: 'heroicons:device-phone-mobile',
    description: '暂时没有电子产品，请稍后再试'
  },
  daily: {
    title: '暂无日用品',
    icon: 'heroicons:shopping-cart',
    description: '暂时没有日用品，请稍后再试'
  },
  furniture: {
    title: '暂无家具家电',
    icon: 'heroicons:home',
    description: '暂时没有家具家电，请稍后再试'
  },
  other: {
    title: '暂无其他商品',
    icon: 'heroicons:cube',
    description: '暂时没有其他商品，请稍后再试'
  }
}

const openContactModal = (item: Item) => {
  selectedItem.value = item
  showContactModal.value = true
}

const copyToClipboard = async (text: string, type: 'wechat' | 'phone') => {
  try {
    await navigator.clipboard.writeText(text)
    copySuccess.value[type] = true
    setTimeout(() => {
      copySuccess.value[type] = false
    }, 2000)
  } catch (err) {
    console.error('复制失败:', err)
  }
}

const getConditionLabel = (condition: string) => {
  const labels = {
    new: '全新',
    like_new: '几乎全新',
    good: '良好',
    fair: '一般',
    poor: '较差'
  }
  return labels[condition as keyof typeof labels] || condition
}

const getConditionColor = (condition: string) => {
  const colors = {
    new: '#52c41a',
    like_new: '#1890ff',
    good: '#722ed1',
    fair: '#fa8c16',
    poor: '#f5222d'
  }
  return colors[condition as keyof typeof colors] || '#666'
}
</script>

<template>
  <div class="sale-page">
    <Topbar />
    <SearchBar />
    
    <div class="sale-container">
      <!-- 分类筛选 -->
      <div class="filter-section">
        <div class="filter-tabs">
          <button
            v-for="cat in categories"
            :key="cat.id"
            class="filter-tab"
            :class="{ active: category === cat.id }"
            @click="category = cat.id"
          >
            <Icon :name="cat.icon" class="tab-icon" />
            {{ cat.label }}
          </button>
        </div>
      </div>

      <!-- 发布按钮 -->
      <button class="new-item-btn" @click="showNewItemModal = true">
        <Icon name="heroicons:plus" />
        发布商品
      </button>

      <!-- 加载状态 -->
      <div v-if="isLoading" class="loading-state">
        <div class="loading-spinner">
          <Icon name="heroicons:arrow-path" class="animate-spin" />
        </div>
        <p>加载中...</p>
      </div>

      <!-- 商品列表 -->
      <div v-else class="items-grid">
        <template v-if="filteredItems.length > 0">
          <div v-for="item in filteredItems" :key="item.id" class="item-card">
            <!-- 图片轮播 -->
            <div class="item-images">
              <div class="image-slider">
                <div
                  v-for="(color, index) in item.images"
                  :key="index"
                  class="image-item"
                  :style="{ backgroundColor: color }"
                >
                  <div class="image-placeholder">
                    <Icon name="heroicons:photo" />
                  </div>
                </div>
              </div>
              <div class="image-dots">
                <span
                  v-for="(_, index) in item.images"
                  :key="index"
                  class="dot"
                  :class="{ active: index === 0 }"
                />
              </div>
            </div>

            <!-- 商品信息 -->
            <div class="item-info">
              <div class="item-header">
                <h3 class="item-title">{{ item.title }}</h3>
                <span 
                  class="condition-tag"
                  :style="{ backgroundColor: getConditionColor(item.condition) }"
                >
                  {{ getConditionLabel(item.condition) }}
                </span>
              </div>
              
              <div class="item-price">{{ item.price }}</div>
              
              <div class="item-location">
                <Icon name="heroicons:map-pin" />
                {{ item.location }}
              </div>

              <div class="item-description">
                {{ item.description }}
              </div>

              <!-- 联系按钮 -->
              <div class="contact-buttons">
                <button class="contact-btn wechat" @click="openContactModal(item)">
                  <Icon name="ri:wechat-fill" />
                  微信联系
                </button>
                <button class="contact-btn phone" @click="openContactModal(item)">
                  <Icon name="heroicons:phone" />
                  电话联系
                </button>
              </div>
            </div>
          </div>
        </template>

        <!-- 空状态 -->
        <div v-else class="empty-state">
          <div class="empty-icon">
            <Icon :name="emptyStateData[category as keyof typeof emptyStateData]?.icon" />
          </div>
          <h3>{{ emptyStateData[category as keyof typeof emptyStateData]?.title }}</h3>
          <p>{{ emptyStateData[category as keyof typeof emptyStateData]?.description }}</p>
        </div>
      </div>
    </div>

    <!-- 联系信息弹窗 -->
    <Transition name="fade">
      <div v-if="showContactModal" class="contact-modal" @click="showContactModal = false">
        <div class="modal-content" @click.stop>
          <button class="close-btn" @click="showContactModal = false">
            <Icon name="heroicons:x-mark" />
          </button>
          <h3>联系方式</h3>
          <div class="contact-info">
            <div class="contact-item">
              <Icon name="ri:wechat-fill" />
              <span>微信：{{ selectedItem?.contact.wechat }}</span>
              <button 
                class="copy-btn"
                @click="copyToClipboard(selectedItem?.contact.wechat || '', 'wechat')"
              >
                <Icon :name="copySuccess.wechat ? 'heroicons:check' : 'heroicons:clipboard'" />
                {{ copySuccess.wechat ? '已复制' : '复制' }}
              </button>
            </div>
            <div class="contact-item">
              <Icon name="heroicons:phone" />
              <span>电话：{{ selectedItem?.contact.phone }}</span>
              <button 
                class="copy-btn"
                @click="copyToClipboard(selectedItem?.contact.phone || '', 'phone')"
              >
                <Icon :name="copySuccess.phone ? 'heroicons:check' : 'heroicons:clipboard'" />
                {{ copySuccess.phone ? '已复制' : '复制' }}
              </button>
            </div>
          </div>
        </div>
      </div>
    </Transition>

    <!-- 发布商品弹窗 -->
    <Transition name="fade">
      <div v-if="showNewItemModal" class="modal" @click="showNewItemModal = false">
        <div class="modal-content" @click.stop>
          <button class="close-btn" @click="resetForm">
            <Icon name="heroicons:x-mark" />
          </button>
          <h3>发布商品</h3>
          <div class="form-group">
            <label>商品分类</label>
            <select v-model="newItem.category" class="form-select">
              <option v-for="cat in categories.slice(1)" :key="cat.id" :value="cat.id">
                {{ cat.label }}
              </option>
            </select>
          </div>
          <div class="form-group">
            <label>商品状态</label>
            <select v-model="newItem.condition" class="form-select">
              <option value="new">全新</option>
              <option value="like_new">几乎全新</option>
              <option value="good">良好</option>
              <option value="fair">一般</option>
              <option value="poor">较差</option>
            </select>
          </div>
          <div class="form-group">
            <label>标题</label>
            <input v-model="newItem.title" type="text" class="form-input" placeholder="请输入商品标题">
          </div>
          <div class="form-group">
            <label>价格</label>
            <input v-model="newItem.price" type="text" class="form-input" placeholder="例如：€800">
          </div>
          <div class="form-group">
            <label>位置</label>
            <input v-model="newItem.location" type="text" class="form-input" placeholder="例如：都柏林2区">
          </div>
          <div class="form-group">
            <label>描述</label>
            <textarea v-model="newItem.description" class="form-textarea" rows="4" placeholder="请详细描述商品情况"/>
          </div>
          <div class="form-group">
            <label>上传图片（最多9张）</label>
            <div class="image-upload-area">
              <input
                type="file"
                accept="image/*"
                multiple
                class="image-input"
                @change="handleImageUpload"
              >
              <div class="upload-placeholder">
                <Icon name="heroicons:photo" />
                <span>点击或拖拽图片到此处</span>
              </div>
            </div>
            <div v-if="newItem.images.length" class="image-preview-list">
              <div
                v-for="(url, index) in newItem.images"
                :key="index"
                class="preview-item"
              >
                <img :src="url" alt="预览图片">
                <button class="remove-image" @click="removeImage(index)">
                  <Icon name="heroicons:x-mark" />
                </button>
              </div>
            </div>
          </div>
          <div class="form-group">
            <label>联系方式</label>
            <div class="contact-inputs">
              <input v-model="newItem.contact.wechat" type="text" class="form-input" placeholder="微信号">
              <input v-model="newItem.contact.phone" type="text" class="form-input" placeholder="手机号">
            </div>
          </div>
          <button class="submit-btn">发布</button>
        </div>
      </div>
    </Transition>

    <!-- 加载更多状态 -->
    <div v-if="isLoadingMore" class="loading-more">
      <div class="loading-spinner">
        <Icon name="heroicons:arrow-path" class="animate-spin" />
      </div>
      <p>加载更多...</p>
    </div>
    
    <!-- 没有更多数据提示 -->
    <div v-if="!hasMore && filteredItems.length > 0" class="no-more">
      <p>没有更多数据了</p>
    </div>
  </div>
</template>

<style scoped>
.sale-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.sale-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px var(--default-padding-x);
}

/* 筛选部分样式 */
.filter-section {
  margin-bottom: 30px;
  background: white;
  padding: 15px;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.filter-tabs {
  display: flex;
  gap: 10px;
  overflow-x: auto;
  padding-bottom: 5px;
}
/* 弹窗样式 */
.modal {
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
  max-height: 100%;
  overflow: auto;
}
.filter-tab {
  padding: 8px 20px;
  border: none;
  background: none;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.9rem;
  color: #666;
  transition: all 0.3s ease;
  white-space: nowrap;
  display: flex;
  align-items: center;
  gap: 6px;
}

.tab-icon {
  width: 16px;
  height: 16px;
}

.filter-tab:hover {
  background-color: #f0f0f0;
  color: var(--primary-color);
}

.filter-tab.active {
  background-color: var(--primary-color);
  color: white;
}

/* 商品列表样式 */
.items-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}

.item-card {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease;
}

.item-card:hover {
  transform: translateY(-4px);
}

.item-images {
  position: relative;
  height: 200px;
  overflow: hidden;
}

.image-slider {
  display: flex;
  height: 100%;
  transition: transform 0.3s ease;
}

.image-item {
  flex: 0 0 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.image-placeholder {
  color: #999;
  font-size: 2rem;
}

.image-dots {
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 6px;
}

.dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.5);
  transition: all 0.3s ease;
}

.dot.active {
  background: white;
  transform: scale(1.2);
}

.item-info {
  padding: 20px;
}

.item-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 10px;
}

.item-title {
  font-size: 1.2rem;
  color: #333;
  margin: 0;
  flex: 1;
  margin-right: 10px;
}

.condition-tag {
  padding: 2px 8px;
  border-radius: 12px;
  font-size: 0.75rem;
  color: white;
  white-space: nowrap;
}

.item-price {
  font-size: 1.4rem;
  color: var(--primary-color);
  font-weight: 600;
  margin-bottom: 10px;
}

.item-location {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  font-size: 0.9rem;
  margin-bottom: 10px;
}

.item-description {
  color: #666;
  font-size: 0.9rem;
  margin-bottom: 15px;
  line-height: 1.5;
}

.contact-buttons {
  display: flex;
  gap: 10px;
  margin-top: 15px;
}

.contact-btn {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 10px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.contact-btn.wechat {
  background: #07c160;
  color: white;
}

.contact-btn.phone {
  background: var(--primary-color);
  color: white;
}

.contact-btn:hover {
  opacity: 0.9;
  transform: translateY(-2px);
}

/* 联系信息弹窗 */
.contact-modal {
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
  padding: 30px;
  border-radius: 12px;
  position: relative;
  width: 90%;
  max-width: 400px;
}

.close-btn {
  position: absolute;
  top: 15px;
  right: 15px;
  background: none;
  border: none;
  cursor: pointer;
  color: #666;
  font-size: 1.2rem;
}

.modal-content h3 {
  margin: 0 0 20px;
  color: #333;
}

.contact-info {
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.contact-item {
  display: flex;
  align-items: center;
  gap: 10px;
  color: #666;
  position: relative;
}

.copy-btn {
  margin-left: auto;
  padding: 4px 8px;
  border: none;
  border-radius: 4px;
  background: #f0f0f0;
  color: #666;
  font-size: 0.8rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 4px;
  transition: all 0.3s ease;
}

.copy-btn:hover {
  background: #e0e0e0;
}

.copy-btn :deep(svg) {
  width: 14px;
  height: 14px;
}

.copy-btn:active {
  transform: scale(0.95);
}

/* 动画效果 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .sale-container {
    padding: 10px;
  }

  .items-grid {
    grid-template-columns: 1fr;
  }

  .filter-tabs {
    padding-bottom: 10px;
  }

  .filter-tab {
    padding: 6px 16px;
    font-size: 0.85rem;
  }

  .item-images {
    height: 180px;
  }

  .item-info {
    padding: 15px;
  }

  .item-title {
    font-size: 1.1rem;
  }

  .item-price {
    font-size: 1.2rem;
  }

  .contact-buttons {
    flex-direction: column;
  }
}

/* 加载状态 */
.loading-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 400px;
  gap: 20px;
  color: #666;
}

.loading-spinner {
  font-size: 2rem;
  color: var(--primary-color);
}

/* 空状态 */
.empty-state {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 400px;
  text-align: center;
  padding: 40px 20px;
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.empty-icon {
  font-size: 3rem;
  color: #ddd;
  margin-bottom: 20px;
}

.empty-state h3 {
  font-size: 1.2rem;
  color: #333;
  margin: 0 0 10px;
}

.empty-state p {
  color: #666;
  margin: 0;
}

/* 发布按钮 */
.new-item-btn {
  position: fixed;
  right: 20px;
  bottom: 20px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 50%;
  width: 56px;
  height: 56px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  font-size: 1.5rem;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
  z-index: 100;
}

.new-item-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
}

/* 表单样式 */
.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  color: #333;
  font-weight: 500;
}

.form-select,
.form-input,
.form-textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 0.95rem;
  transition: border-color 0.3s ease;
}

.form-select:focus,
.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: var(--primary-color);
}

.form-textarea {
  resize: vertical;
}

.contact-inputs {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.submit-btn {
  width: 100%;
  padding: 12px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
}

.submit-btn:hover {
  opacity: 0.9;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .contact-inputs {
    grid-template-columns: 1fr;
  }
}

/* 图片上传 */
.image-upload-area {
  position: relative;
  width: 100%;
  height: 120px;
  border: 2px dashed #ddd;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.image-upload-area:hover {
  border-color: var(--primary-color);
}

.image-input {
  position: absolute;
  width: 100%;
  height: 100%;
  opacity: 0;
  cursor: pointer;
}

.upload-placeholder {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  color: #999;
}

.upload-placeholder :deep(svg) {
  width: 24px;
  height: 24px;
}

.image-preview-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  gap: 10px;
  margin-top: 10px;
}

.preview-item {
  position: relative;
  aspect-ratio: 1;
  border-radius: 8px;
  overflow: hidden;
}

.preview-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.remove-image {
  position: absolute;
  top: 5px;
  right: 5px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.5);
  color: white;
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.remove-image:hover {
  background: rgba(0, 0, 0, 0.7);
}

/* 响应式调整 */
@media (max-width: 768px) {
  .image-preview-list {
    grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
  }
}

/* 加载更多状态 */
.loading-more {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 20px;
  color: #666;
}

.loading-more .loading-spinner {
  font-size: 1.5rem;
  margin-bottom: 10px;
}

/* 没有更多数据提示 */
.no-more {
  text-align: center;
  padding: 20px;
  color: #999;
  font-size: 0.9rem;
}
</style>
