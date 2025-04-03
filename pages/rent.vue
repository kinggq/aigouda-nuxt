<script setup lang="ts">
import { onMounted, onUnmounted } from 'vue'

const route = useRoute()
const rentalType = ref(route.query.type || 'all')

const rentalTypes = [
  { id: 'all', label: '全部', icon: 'heroicons:home' },
  { id: 'whole', label: '整租', icon: 'heroicons:home' },
  { id: 'shared', label: '合租', icon: 'heroicons:user-group' },
  { id: 'short', label: '短租', icon: 'heroicons:clock' }
]

interface House {
  id: number;
  title: string;
  price: string;
  location: string;
  type: string;
  hasBill: boolean;
  images: string[];
  busStops: string[];
  contact: {
    wechat: string;
    phone: string;
  }
}

const houses = ref<House[]>([
  {
    id: 1,
    title: '温馨双人间，近市中心',
    price: '€1,200/月',
    location: '都柏林2区',
    type: 'shared',
    hasBill: true,
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    busStops: ['距离Luas站5分钟', '距离公交站3分钟'],
    contact: {
      wechat: 'house_owner_1',
      phone: '+353 87 123 4567'
    }
  },
  {
    id: 2,
    title: '豪华公寓整租',
    price: '€2,500/月',
    location: '都柏林4区',
    type: 'whole',
    hasBill: false,
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    busStops: ['距离Dart站8分钟', '距离公交站2分钟'],
    contact: {
      wechat: 'house_owner_2',
      phone: '+353 87 234 5678'
    }
  },
  {
    id: 3,
    title: '短租单人间，拎包入住',
    price: '€800/周',
    location: '都柏林1区',
    type: 'short',
    hasBill: true,
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    busStops: ['距离Luas站1分钟', '距离公交站2分钟'],
    contact: {
      wechat: 'house_owner_3',
      phone: '+353 87 345 6789'
    }
  }
])

const filteredHouses = computed(() => {
  if (rentalType.value === 'all') return houses.value
  return houses.value.filter(house => house.type === rentalType.value)
})

const showContactModal = ref(false)
const showNewHouseModal = ref(false)
const selectedItem = ref<House | null>(null)
const copySuccess = ref({
  wechat: false,
  phone: false
})
const isLoading = ref(true)
const newHouse = ref({
  title: '',
  price: '',
  location: '',
  type: 'whole',
  hasBill: false,
  images: [] as string[],
  busStops: [''],
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
const loadMoreHouses = async () => {
  if (isLoadingMore.value || !hasMore.value) return
  
  isLoadingMore.value = true
  try {
    // 模拟API请求延迟
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // 模拟新数据
    const newHouses = Array.from({ length: 5 }, (_, index) => ({
      id: houses.value.length + index + 1,
      title: `新房源 ${houses.value.length + index + 1}`,
      price: `€${Math.floor(Math.random() * 1000 + 800)}/月`,
      location: '都柏林2区',
      type: rentalTypes[Math.floor(Math.random() * 3) + 1].id,
      hasBill: Math.random() > 0.5,
      images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
      busStops: ['距离Luas站5分钟', '距离公交站3分钟'],
      contact: {
        wechat: `house_owner_${houses.value.length + index + 1}`,
        phone: `+353 87 ${Math.floor(Math.random() * 9000000 + 1000000)}`
      }
    }))
    
    houses.value.push(...newHouses)
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
    loadMoreHouses()
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

// 处理图片上传
const handleImageUpload = (event: Event) => {
  const input = event.target as HTMLInputElement
  if (input.files) {
    const files = Array.from(input.files)
    if (files.length + newHouse.value.images.length > 9) {
      alert('最多只能上传9张图片')
      return
    }
    
    files.forEach(file => {
      const reader = new FileReader()
      reader.onload = (e) => {
        newHouse.value.images.push(e.target?.result as string)
      }
      reader.readAsDataURL(file)
    })
  }
}

// 移除预览图片
const removeImage = (index: number) => {
  newHouse.value.images.splice(index, 1)
}

// 清空表单
const resetForm = () => {
  newHouse.value = {
    title: '',
    price: '',
    location: '',
    type: 'whole',
    hasBill: false,
    images: [],
    busStops: [''],
    contact: {
      wechat: '',
      phone: ''
    }
  }
  showNewHouseModal.value = false
}

// 空状态数据
const emptyStateData = {
  all: {
    title: '暂无房源',
    icon: 'heroicons:home',
    description: '暂时没有可用的房源，请稍后再试'
  },
  whole: {
    title: '暂无整租',
    icon: 'heroicons:home',
    description: '暂时没有整租房源，请稍后再试'
  },
  shared: {
    title: '暂无合租',
    icon: 'heroicons:user-group',
    description: '暂时没有合租房源，请稍后再试'
  },
  short: {
    title: '暂无短租',
    icon: 'heroicons:clock',
    description: '暂时没有短租房源，请稍后再试'
  }
}

const openContactModal = (house: House) => {
  selectedItem.value = house
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
</script>

<template>
  <div class="rent-page">
    <Topbar />
    <SearchBar />
    
    <div class="rent-container">
      <!-- 筛选部分 -->
      <div class="filter-section">
        <div class="filter-tabs">
          <button
            v-for="type in rentalTypes"
            :key="type.id"
            class="filter-tab"
            :class="{ active: rentalType === type.id }"
            @click="rentalType = type.id"
          >
            <Icon :name="type.icon" class="tab-icon" />
            {{ type.label }}
          </button>
        </div>
      </div>

      <!-- 发布按钮 -->
      <button class="new-house-btn" @click="showNewHouseModal = true">
        <Icon name="heroicons:plus" />
        发布房源
      </button>

      <!-- 加载状态 -->
      <div v-if="isLoading" class="loading-state">
        <div class="loading-spinner">
          <Icon name="heroicons:arrow-path" class="animate-spin" />
        </div>
        <p>加载中...</p>
      </div>

      <!-- 房源列表 -->
      <div class="houses-grid">
        <template v-if="filteredHouses.length > 0">
          <div v-for="house in filteredHouses" :key="house.id" class="house-card">
            <!-- 图片轮播 -->
            <div class="house-images">
              <div class="image-slider">
                <div
v-for="(color, index) in house.images" :key="index" 
                     class="image-item" :style="{ backgroundColor: color }">
                  <div class="image-placeholder">
                    <Icon name="heroicons:photo" />
                  </div>
                </div>
              </div>
              <div class="image-dots">
                <span
v-for="(_, index) in house.images" :key="index" 
                      class="dot" :class="{ active: index === 0 }"/>
              </div>
            </div>

            <!-- 房源信息 -->
            <div class="house-info">
              <h3 class="house-title">{{ house.title }}</h3>
              <div class="house-price">{{ house.price }}</div>
              <div class="house-location">
                <Icon name="heroicons:map-pin" />
                {{ house.location }}
              </div>
              
              <!-- 交通信息 -->
              <div class="house-transport">
                <Icon name="heroicons:bus" />
                <div class="transport-info">
                  <div v-for="(stop, index) in house.busStops" :key="index">
                    {{ stop }}
                  </div>
                </div>
              </div>

              <!-- 其他信息 -->
              <div class="house-features">
                <span class="feature-tag" :class="{ 'has-bill': house.hasBill }">
                  {{ house.hasBill ? '包Bill' : '不包Bill' }}
                </span>
              </div>

              <!-- 联系按钮 -->
              <div class="contact-buttons">
                <button class="contact-btn wechat" @click="openContactModal(house)">
                  <Icon name="ri:wechat-fill" />
                  微信联系
                </button>
                <button class="contact-btn phone" @click="openContactModal(house)">
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
            <Icon :name="emptyStateData[rentalType as keyof typeof emptyStateData]?.icon" />
          </div>
          <h3>{{ emptyStateData[rentalType as keyof typeof emptyStateData]?.title }}</h3>
          <p>{{ emptyStateData[rentalType as keyof typeof emptyStateData]?.description }}</p>
        </div>
      </div>

      <!-- 没有更多数据提示 -->
      <div v-if="!hasMore && filteredHouses.length > 0" class="no-more">
        <p>没有更多数据了</p>
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

    <!-- 发布房源弹窗 -->
    <Transition name="fade">
      <div v-if="showNewHouseModal" class="modal" @click="showNewHouseModal = false">
        <div class="modal-content" @click.stop>
          <button class="close-btn" @click="resetForm">
            <Icon name="heroicons:x-mark" />
          </button>
          <h3>发布房源</h3>
          <div class="form-group">
            <label>房源类型</label>
            <select v-model="newHouse.type" class="form-select">
              <option v-for="type in rentalTypes.slice(1)" :key="type.id" :value="type.id">
                {{ type.label }}
              </option>
            </select>
          </div>
          <div class="form-group">
            <label>标题</label>
            <input v-model="newHouse.title" type="text" class="form-input" placeholder="请输入房源标题">
          </div>
          <div class="form-group">
            <label>价格</label>
            <input v-model="newHouse.price" type="text" class="form-input" placeholder="例如：€1,200/月">
          </div>
          <div class="form-group">
            <label>位置</label>
            <input v-model="newHouse.location" type="text" class="form-input" placeholder="例如：都柏林2区">
          </div>
          <div class="form-group">
            <label>交通信息</label>
            <div v-for="(stop, index) in newHouse.busStops" :key="index" class="bus-stop-input">
              <input v-model="newHouse.busStops[index]" type="text" class="form-input" placeholder="例如：距离Luas站5分钟">
              <button v-if="index === newHouse.busStops.length - 1" class="add-stop-btn" @click="newHouse.busStops.push('')">
                <Icon name="heroicons:plus" />
              </button>
              <button v-else class="remove-stop-btn" @click="newHouse.busStops.splice(index, 1)">
                <Icon name="heroicons:minus" />
              </button>
            </div>
          </div>
          <div class="form-group">
            <label class="checkbox-label">
              <input v-model="newHouse.hasBill" type="checkbox">
              包含水电费
            </label>
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
            <div v-if="newHouse.images.length" class="image-preview-list">
              <div
                v-for="(url, index) in newHouse.images"
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
              <input v-model="newHouse.contact.wechat" type="text" class="form-input" placeholder="微信号">
              <input v-model="newHouse.contact.phone" type="text" class="form-input" placeholder="手机号">
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
  </div>
</template>

<style scoped>
.rent-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.rent-container {
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
}

.filter-tab:hover {
  background-color: #f0f0f0;
  color: var(--primary-color);
}

.filter-tab.active {
  background-color: var(--primary-color);
  color: white;
}

/* 房源列表样式 */
.houses-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
}

.house-card {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease;
}

.house-card:hover {
  transform: translateY(-4px);
}

.house-images {
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

.house-info {
  padding: 20px;
}

.house-title {
  font-size: 1.2rem;
  color: #333;
  margin: 0 0 10px;
}

.house-price {
  font-size: 1.4rem;
  color: var(--primary-color);
  font-weight: 600;
  margin-bottom: 10px;
}

.house-location,
.house-transport {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  font-size: 0.9rem;
  margin-bottom: 10px;
}

.transport-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.house-features {
  margin: 15px 0;
}

.feature-tag {
  display: inline-block;
  padding: 4px 12px;
  border-radius: 16px;
  font-size: 0.8rem;
  background: #f0f0f0;
  color: #666;
}

.feature-tag.has-bill {
  background: #e6f7ff;
  color: var(--primary-color);
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
  .rent-container {
    padding: 10px;
  }

  .houses-grid {
    grid-template-columns: 1fr;
  }

  .filter-tabs {
    padding-bottom: 10px;
  }

  .filter-tab {
    padding: 6px 16px;
    font-size: 0.85rem;
  }

  .house-images {
    height: 180px;
  }

  .house-info {
    padding: 15px;
  }

  .house-title {
    font-size: 1.1rem;
  }

  .house-price {
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
.new-house-btn {
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

.new-house-btn:hover {
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
.form-input {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 0.95rem;
  transition: border-color 0.3s ease;
}

.form-select:focus,
.form-input:focus {
  outline: none;
  border-color: var(--primary-color);
}

.bus-stop-input {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
}

.add-stop-btn,
.remove-stop-btn {
  width: 36px;
  height: 36px;
  border: none;
  border-radius: 8px;
  background: #f0f0f0;
  color: #666;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.3s ease;
}

.add-stop-btn:hover,
.remove-stop-btn:hover {
  background: #e0e0e0;
}

.checkbox-label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.checkbox-label input[type="checkbox"] {
  width: 16px;
  height: 16px;
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
