<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const isLoading = ref(true)
const isLoadingMore = ref(false)
const hasMore = ref(true)
const pageSize = ref(20)
const currentPage = ref(1)
const showNewGuideModal = ref(false)

interface Guide {
  id: number
  title: string
  category: string
  author: {
    name: string
    avatar: string
  }
  cover: string
  summary: string
  stats: {
    views: number
    likes: number
    comments: number
  }
  createdAt: string
}

// 模拟攻略数据
const guides = ref<Guide[]>([
  {
    id: 1,
    title: '都柏林生活指南：从租房到交通全攻略',
    category: 'life',
    author: {
      name: '小明',
      avatar: '#FFE5E5'
    },
    cover: '#FFE5E5',
    summary: '本文详细介绍了在都柏林生活的方方面面，包括租房、交通、购物等实用信息，帮助新来的同学快速适应都柏林的生活。',
    stats: {
      views: 1280,
      likes: 45,
      comments: 32
    },
    createdAt: '2024-03-15 14:30'
  },
  {
    id: 2,
    title: '爱尔兰留学申请全攻略',
    category: 'study',
    author: {
      name: '小红',
      avatar: '#E5FFE5'
    },
    cover: '#E5FFE5',
    summary: '从选校到签证，从住宿到生活，全方位解析爱尔兰留学申请流程，助你顺利开启留学之旅。',
    stats: {
      views: 960,
      likes: 38,
      comments: 28
    },
    createdAt: '2024-03-14 16:20'
  }
])

// 新攻略表单
const newGuide = ref({
  title: '',
  category: '',
  cover: '',
  content: '',
  summary: ''
})

// 分类选项
const categories = [
  { value: 'life', label: '生活服务' },
  { value: 'study', label: '学习交流' },
  { value: 'job', label: '求职招聘' },
  { value: 'travel', label: '旅游攻略' },
  { value: 'food', label: '美食推荐' }
]

// 模拟加载延迟
onMounted(() => {
  setTimeout(() => {
    isLoading.value = false
  }, 1500)
  
  // 添加滚动监听
  window.addEventListener('scroll', handleScroll)
})

onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})

// 处理滚动加载
const handleScroll = () => {
  if (isLoadingMore.value || !hasMore.value) return
  
  const scrollHeight = document.documentElement.scrollHeight
  const scrollTop = document.documentElement.scrollTop
  const clientHeight = document.documentElement.clientHeight
  
  if (scrollHeight - scrollTop - clientHeight < 100) {
    loadMoreGuides()
  }
}

// 加载更多攻略
const loadMoreGuides = async () => {
  if (isLoadingMore.value) return
  
  isLoadingMore.value = true
  
  // 模拟API请求延迟
  await new Promise(resolve => setTimeout(resolve, 1000))
  
  // 模拟新数据
  const newGuides: Guide[] = [
    {
      id: guides.value.length + 1,
      title: '爱尔兰工作签证申请指南',
      category: 'job',
      author: {
        name: '小张',
        avatar: '#E5E5FF'
      },
      cover: '#E5E5FF',
      summary: '详细介绍了爱尔兰工作签证的申请流程、所需材料及注意事项，帮助求职者顺利获得工作签证。',
      stats: {
        views: 850,
        likes: 32,
        comments: 25
      },
      createdAt: '2024-03-13 09:15'
    }
  ]
  
  guides.value.push(...newGuides)
  currentPage.value++
  
  // 模拟数据加载完毕
  if (currentPage.value >= 3) {
    hasMore.value = false
  }
  
  isLoadingMore.value = false
}

// 发布新攻略
const submitGuide = () => {
  if (!newGuide.value.title || !newGuide.value.category || !newGuide.value.content) {
    alert('请填写完整信息')
    return
  }
  
  const guide: Guide = {
    id: guides.value.length + 1,
    title: newGuide.value.title,
    category: newGuide.value.category,
    author: {
      name: '当前用户',
      avatar: '#E5FFE5'
    },
    cover: newGuide.value.cover || '#E5FFE5',
    summary: newGuide.value.summary,
    stats: {
      views: 0,
      likes: 0,
      comments: 0
    },
    createdAt: '刚刚'
  }
  
  guides.value.unshift(guide)
  showNewGuideModal.value = false
  
  // 重置表单
  newGuide.value = {
    title: '',
    category: '',
    cover: '',
    content: '',
    summary: ''
  }
}

// 获取分类图标
const getCategoryIcon = (category: string) => {
  const icons = {
    life: 'heroicons:home',
    study: 'heroicons:academic-cap',
    job: 'heroicons:briefcase',
    travel: 'heroicons:map',
    food: 'heroicons:cake'
  }
  return icons[category as keyof typeof icons] || 'heroicons:square-2-stack'
}

// 获取分类标签
const getCategoryLabel = (category: string) => {
  return categories.find(c => c.value === category)?.label || '其他'
}

// 格式化数字
const formatNumber = (num: number) => {
  if (num >= 1000) {
    return (num / 1000).toFixed(1) + 'k'
  }
  return num.toString()
}

// 跳转到详情页
const goToDetail = (id: number) => {
  router.push(`/guide/${id}`)
}
</script>

<template>
  <div class="guide-page">
    <Topbar />
    <SearchBar />
    
    <div class="guide-container">
      <!-- 页面标题 -->
      <div class="page-header">
        <h1 class="page-title">
          <Icon name="heroicons:book-open" />
          攻略中心
        </h1>
        <button 
          class="publish-btn"
          @click="showNewGuideModal = true"
        >
          <Icon name="heroicons:pencil-square" />
          发布攻略
        </button>
      </div>

      <!-- 加载状态 -->
      <div v-if="isLoading" class="loading-state">
        <div class="loading-spinner">
          <Icon name="heroicons:arrow-path" class="animate-spin" />
        </div>
        <p>加载中...</p>
      </div>

      <!-- 攻略列表 -->
      <div v-else class="guide-list">
        <div 
          v-for="guide in guides" 
          :key="guide.id"
          class="guide-item"
          @click="goToDetail(guide.id)"
        >
          <div class="guide-cover" :style="{ backgroundColor: guide.cover }">
            <div class="cover-placeholder">
              <Icon name="heroicons:photo" />
            </div>
          </div>
          <div class="guide-content">
            <div class="guide-header">
              <div class="guide-category">
                <Icon :name="getCategoryIcon(guide.category)" />
                {{ getCategoryLabel(guide.category) }}
              </div>
              <div class="guide-stats">
                <div class="stat-item">
                  <Icon name="heroicons:eye" />
                  {{ formatNumber(guide.stats.views) }}
                </div>
                <div class="stat-item">
                  <Icon name="heroicons:heart" />
                  {{ formatNumber(guide.stats.likes) }}
                </div>
                <div class="stat-item">
                  <Icon name="heroicons:chat-bubble-left" />
                  {{ formatNumber(guide.stats.comments) }}
                </div>
              </div>
            </div>
            <h2 class="guide-title">{{ guide.title }}</h2>
            <p class="guide-summary">{{ guide.summary }}</p>
            <div class="guide-footer">
              <div class="author-info">
                <div class="avatar" :style="{ backgroundColor: guide.author.avatar }">
                  {{ guide.author.name[0] }}
                </div>
                <span class="author-name">{{ guide.author.name }}</span>
              </div>
              <span class="publish-time">{{ guide.createdAt }}</span>
            </div>
          </div>
        </div>
      </div>

      <!-- 加载更多 -->
      <div v-if="!isLoading" class="load-more">
        <div v-if="isLoadingMore" class="loading-more">
          <Icon name="heroicons:arrow-path" class="animate-spin" />
          <span>加载中...</span>
        </div>
        <div v-else-if="!hasMore" class="no-more">
          没有更多攻略了
        </div>
      </div>
    </div>

    <!-- 发布攻略弹窗 -->
    <div v-if="showNewGuideModal" class="modal-overlay" @click="showNewGuideModal = false">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h2 class="modal-title">发布攻略</h2>
          <button class="close-btn" @click="showNewGuideModal = false">
            <Icon name="heroicons:x-mark" />
          </button>
        </div>
        <div class="modal-body">
          <div class="form-group">
            <label class="form-label">标题</label>
            <input 
              v-model="newGuide.title"
              type="text"
              class="form-input"
              placeholder="请输入攻略标题"
            >
          </div>
          <div class="form-group">
            <label class="form-label">分类</label>
            <select 
              v-model="newGuide.category"
              class="form-select"
            >
              <option value="">请选择分类</option>
              <option 
                v-for="category in categories"
                :key="category.value"
                :value="category.value"
              >
                {{ category.label }}
              </option>
            </select>
          </div>
          <div class="form-group">
            <label class="form-label">封面图片</label>
            <div class="cover-upload">
              <div 
                class="cover-preview"
                :style="{ backgroundColor: newGuide.cover || '#f0f0f0' }"
              >
                <Icon name="heroicons:photo" />
              </div>
              <input 
                type="file"
                accept="image/*"
                class="hidden"
              >
            </div>
          </div>
          <div class="form-group">
            <label class="form-label">摘要</label>
            <textarea 
              v-model="newGuide.summary"
              class="form-textarea"
              rows="3"
              placeholder="请输入攻略摘要"
            />
          </div>
          <div class="form-group">
            <label class="form-label">内容</label>
            <textarea 
              v-model="newGuide.content"
              class="form-textarea"
              rows="10"
              placeholder="请输入攻略内容"
            />
          </div>
        </div>
        <div class="modal-footer">
          <button 
            class="cancel-btn"
            @click="showNewGuideModal = false"
          >
            取消
          </button>
          <button 
            class="submit-btn"
            @click="submitGuide"
          >
            发布
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.guide-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.guide-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px var(--default-padding-x);
}

/* 页面标题 */
.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;
}

.page-title {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 1.8rem;
  color: #333;
}

.publish-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 20px;
  border: none;
  background: var(--primary-color);
  color: white;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.publish-btn:hover {
  opacity: 0.9;
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

/* 攻略列表 */
.guide-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.guide-item {
  display: flex;
  gap: 20px;
  background: white;
  border-radius: 12px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.guide-item:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.guide-cover {
  width: 200px;
  height: 150px;
  border-radius: 8px;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
}

.cover-placeholder {
  color: #999;
  font-size: 2rem;
}

.guide-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.guide-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.guide-category {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 4px 12px;
  background: #f0f0f0;
  border-radius: 20px;
  color: #666;
  font-size: 0.9rem;
}

.guide-stats {
  display: flex;
  gap: 20px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 4px;
  color: #999;
  font-size: 0.9rem;
}

.guide-title {
  font-size: 1.4rem;
  color: #333;
  line-height: 1.4;
}

.guide-summary {
  color: #666;
  font-size: 0.95rem;
  line-height: 1.6;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.guide-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: auto;
}

.author-info {
  display: flex;
  align-items: center;
  gap: 8px;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 0.9rem;
  font-weight: 500;
}

.author-name {
  color: #666;
  font-size: 0.9rem;
}

.publish-time {
  color: #999;
  font-size: 0.9rem;
}

/* 加载更多 */
.load-more {
  margin-top: 30px;
  text-align: center;
  color: #999;
}

.loading-more {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

/* 弹窗 */
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
  border-radius: 12px;
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

.modal-title {
  font-size: 1.4rem;
  color: #333;
}

.close-btn {
  padding: 4px;
  border: none;
  background: none;
  color: #999;
  cursor: pointer;
  transition: all 0.3s ease;
}

.close-btn:hover {
  color: #666;
}

.modal-body {
  padding: 20px;
}

.form-group {
  margin-bottom: 20px;
}

.form-label {
  display: block;
  margin-bottom: 8px;
  color: #333;
  font-size: 0.95rem;
}

.form-input,
.form-select,
.form-textarea {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid #eee;
  border-radius: 8px;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.form-input:focus,
.form-select:focus,
.form-textarea:focus {
  outline: none;
  border-color: var(--primary-color);
}

.form-textarea {
  resize: vertical;
}

.cover-upload {
  display: flex;
  gap: 12px;
}

.cover-preview {
  width: 120px;
  height: 90px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 1.5rem;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  gap: 12px;
  padding: 20px;
  border-top: 1px solid #eee;
}

.cancel-btn {
  padding: 8px 20px;
  border: 1px solid #eee;
  background: none;
  color: #666;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.cancel-btn:hover {
  border-color: #ddd;
  color: #333;
}

.submit-btn {
  padding: 8px 20px;
  border: none;
  background: var(--primary-color);
  color: white;
  border-radius: 20px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.submit-btn:hover {
  opacity: 0.9;
}

.hidden {
  display: none;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .guide-container {
    padding: 10px;
  }

  .page-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 15px;
  }

  .page-title {
    font-size: 1.5rem;
  }

  .publish-btn {
    width: 100%;
    justify-content: center;
  }

  .guide-item {
    flex-direction: column;
  }

  .guide-cover {
    width: 100%;
    height: 200px;
  }

  .guide-stats {
    gap: 12px;
  }

  .guide-title {
    font-size: 1.2rem;
  }

  .modal-content {
    width: 95%;
    margin: 10px;
  }

  .cover-upload {
    flex-direction: column;
  }

  .cover-preview {
    width: 100%;
    height: 150px;
  }
}
</style>
