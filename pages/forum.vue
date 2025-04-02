<script setup lang="ts">
const categories = [
  { id: 'all', label: '全部', icon: 'heroicons:square-2-stack' },
  { id: 'general', label: '综合讨论', icon: 'heroicons:chat-bubble-left-right' },
  { id: 'life', label: '生活服务', icon: 'heroicons:home' },
  { id: 'study', label: '学习交流', icon: 'heroicons:academic-cap' },
  { id: 'job', label: '求职招聘', icon: 'heroicons:briefcase' },
  { id: 'activity', label: '活动聚会', icon: 'heroicons:user-group' }
]

const currentCategory = ref('all')
const showNewPostModal = ref(false)
const isLoading = ref(true)
const selectedImages = ref<File[]>([])
const imagePreviewUrls = ref<string[]>([])

// 分页相关状态
const pageSize = 20
const currentPage = ref(1)
const isLoadingMore = ref(false)
const hasMore = ref(true)

// 模拟加载延迟
onMounted(() => {
  setTimeout(() => {
    isLoading.value = false
  }, 1500)
  window.addEventListener('scroll', handleScroll)
})

interface Post {
  id: number
  title: string
  category: string
  author: {
    name: string
    avatar: string
  }
  content: string
  images?: string[]
  stats: {
    views: number
    replies: number
    likes: number
  }
  lastReply: {
    time: string
    author: string
  }
}

const posts = ref<Post[]>([
  {
    id: 1,
    title: '都柏林租房经验分享',
    category: 'life',
    author: {
      name: '小明',
      avatar: '#FFE5E5'
    },
    content: '最近在都柏林找房，总结了一些经验分享给大家...',
    images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
    stats: {
      views: 1280,
      replies: 32,
      likes: 45
    },
    lastReply: {
      time: '10分钟前',
      author: '小红'
    }
  },
  {
    id: 2,
    title: '寻找Java开发工作机会',
    category: 'job',
    author: {
      name: '张三',
      avatar: '#E5FFE5'
    },
    content: '3年Java开发经验，熟悉Spring Boot...',
    stats: {
      views: 856,
      replies: 15,
      likes: 23
    },
    lastReply: {
      time: '30分钟前',
      author: 'HR小王'
    }
  },
  {
    id: 3,
    title: '周末徒步活动召集',
    category: 'activity',
    author: {
      name: '户外达人',
      avatar: '#E5E5FF'
    },
    content: '本周六组织Wicklow山脉徒步活动...',
    images: ['#FFE5E5', '#E5FFE5'],
    stats: {
      views: 654,
      replies: 28,
      likes: 67
    },
    lastReply: {
      time: '1小时前',
      author: '徒步爱好者'
    }
  }
])

const filteredPosts = computed(() => {
  if (currentCategory.value === 'all') return posts.value
  return posts.value.filter(post => post.category === currentCategory.value)
})

const formatNumber = (num: number) => {
  if (num >= 1000) {
    return (num / 1000).toFixed(1) + 'k'
  }
  return num.toString()
}

// 处理图片上传
const handleImageUpload = (event: Event) => {
  const input = event.target as HTMLInputElement
  if (input.files) {
    const files = Array.from(input.files)
    if (files.length + selectedImages.value.length > 9) {
      alert('最多只能上传9张图片')
      return
    }
    
    selectedImages.value.push(...files)
    files.forEach(file => {
      const reader = new FileReader()
      reader.onload = (e) => {
        imagePreviewUrls.value.push(e.target?.result as string)
      }
      reader.readAsDataURL(file)
    })
  }
}

// 移除预览图片
const removeImage = (index: number) => {
  selectedImages.value.splice(index, 1)
  imagePreviewUrls.value.splice(index, 1)
}

// 清空表单
const resetForm = () => {
  selectedImages.value = []
  imagePreviewUrls.value = []
  showNewPostModal.value = false
}

// 空状态数据
const emptyStateData = {
  general: {
    title: '暂无讨论',
    icon: 'heroicons:chat-bubble-left-right',
    description: '快来发起第一个讨论吧！'
  },
  life: {
    title: '暂无生活服务',
    icon: 'heroicons:home',
    description: '分享你的生活经验，帮助更多人'
  },
  study: {
    title: '暂无学习交流',
    icon: 'heroicons:academic-cap',
    description: '一起学习，共同进步'
  },
  job: {
    title: '暂无招聘信息',
    icon: 'heroicons:briefcase',
    description: '发布你的求职需求或招聘信息'
  },
  activity: {
    title: '暂无活动',
    icon: 'heroicons:user-group',
    description: '组织或参与有趣的活动'
  }
}

// 模拟加载更多数据
const loadMorePosts = async () => {
  if (isLoadingMore.value || !hasMore.value) return
  
  isLoadingMore.value = true
  try {
    // 模拟API请求延迟
    await new Promise(resolve => setTimeout(resolve, 1000))
    
    // 模拟新数据
    const newPosts = Array.from({ length: 5 }, (_, index) => ({
      id: posts.value.length + index + 1,
      title: `新帖子 ${posts.value.length + index + 1}`,
      category: categories[Math.floor(Math.random() * 5) + 1].id,
      author: {
        name: `用户${posts.value.length + index + 1}`,
        avatar: `#${Math.floor(Math.random()*16777215).toString(16)}`
      },
      content: '这是一个示例帖子内容...',
      images: Math.random() > 0.5 ? ['#FFE5E5', '#E5FFE5', '#E5E5FF'] : undefined,
      stats: {
        views: Math.floor(Math.random() * 1000),
        replies: Math.floor(Math.random() * 100),
        likes: Math.floor(Math.random() * 50)
      },
      lastReply: {
        time: '10分钟前',
        author: `回复者${Math.floor(Math.random() * 10)}`
      }
    }))
    
    posts.value.push(...newPosts)
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
    loadMorePosts()
  }
}

// 组件卸载时移除滚动监听
onUnmounted(() => {
  window.removeEventListener('scroll', handleScroll)
})
</script>

<template>
  <div class="forum-page">
    <Topbar />
    <SearchBar />
    
    <div class="forum-container">
      <!-- 分类筛选 -->
      <div class="filter-section">
        <div class="filter-tabs">
          <button
            v-for="cat in categories"
            :key="cat.id"
            class="filter-tab"
            :class="{ active: currentCategory === cat.id }"
            @click="currentCategory = cat.id"
          >
            <Icon :name="cat.icon" class="tab-icon" />
            {{ cat.label }}
          </button>
        </div>
      </div>

      <!-- 发帖按钮 -->
      <button class="new-post-btn" @click="showNewPostModal = true">
        <Icon name="heroicons:plus" />
        发布新帖
      </button>

      <!-- 加载状态 -->
      <div v-if="isLoading" class="loading-state">
        <div class="loading-spinner">
          <Icon name="heroicons:arrow-path" class="animate-spin" />
        </div>
        <p>加载中...</p>
      </div>

      <!-- 帖子列表 -->
      <div v-else class="posts-list">
        <template v-if="filteredPosts.length > 0">
          <div v-for="post in filteredPosts" :key="post.id" class="post-card">
            <div class="post-header">
              <div class="author-info">
                <div class="avatar" :style="{ backgroundColor: post.author.avatar }">
                  {{ post.author.name[0] }}
                </div>
                <div class="author-name">{{ post.author.name }}</div>
              </div>
              <div class="post-category">
                <Icon :name="categories.find(c => c.id === post.category)?.icon" />
                {{ categories.find(c => c.id === post.category)?.label }}
              </div>
            </div>

            <h3 class="post-title">{{ post.title }}</h3>
            <p class="post-content">{{ post.content }}</p>

            <!-- 图片预览 -->
            <div v-if="post.images?.length" class="post-images">
              <div 
                v-for="(image, index) in post.images" 
                :key="index"
                class="image-preview"
                :style="{ backgroundColor: image }"
              >
                <div class="image-placeholder">
                  <Icon name="heroicons:photo" />
                </div>
              </div>
            </div>

            <div class="post-footer">
              <div class="post-stats">
                <div class="stat-item">
                  <Icon name="heroicons:eye" />
                  {{ formatNumber(post.stats.views) }}
                </div>
                <div class="stat-item">
                  <Icon name="heroicons:chat-bubble-left" />
                  {{ formatNumber(post.stats.replies) }}
                </div>
                <div class="stat-item">
                  <Icon name="heroicons:heart" />
                  {{ formatNumber(post.stats.likes) }}
                </div>
              </div>
              <div class="last-reply">
                最后回复：{{ post.lastReply.author }} · {{ post.lastReply.time }}
              </div>
            </div>
          </div>
        </template>

        <!-- 空状态 -->
        <div v-else class="empty-state">
          <div class="empty-icon">
            <Icon :name="emptyStateData[currentCategory as keyof typeof emptyStateData]?.icon" />
          </div>
          <h3>{{ emptyStateData[currentCategory as keyof typeof emptyStateData]?.title }}</h3>
          <p>{{ emptyStateData[currentCategory as keyof typeof emptyStateData]?.description }}</p>
          <button class="create-post-btn" @click="showNewPostModal = true">
            <Icon name="heroicons:plus" />
            发布新帖
          </button>
        </div>
      </div>
    </div>

    <!-- 加载更多状态 -->
    <div v-if="isLoadingMore" class="loading-more">
      <div class="loading-spinner">
        <Icon name="heroicons:arrow-path" class="animate-spin" />
      </div>
      <p>加载更多...</p>
    </div>
    
    <!-- 没有更多数据提示 -->
    <div v-if="!hasMore && filteredPosts.length > 0" class="no-more">
      <p>没有更多数据了</p>
    </div>

    <!-- 发帖弹窗 -->
    <Transition name="fade">
      <div v-if="showNewPostModal" class="modal" @click="showNewPostModal = false">
        <div class="modal-content" @click.stop>
          <button class="close-btn" @click="resetForm">
            <Icon name="heroicons:x-mark" />
          </button>
          <h3>发布新帖</h3>
          <div class="form-group">
            <label>选择分类</label>
            <select class="form-select">
              <option v-for="cat in categories.slice(1)" :key="cat.id" :value="cat.id">
                {{ cat.label }}
              </option>
            </select>
          </div>
          <div class="form-group">
            <label>标题</label>
            <input type="text" class="form-input" placeholder="请输入标题" >
          </div>
          <div class="form-group">
            <label>内容</label>
            <textarea class="form-textarea" rows="6" placeholder="请输入内容"/>
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
            <div v-if="imagePreviewUrls.length" class="image-preview-list">
              <div
                v-for="(url, index) in imagePreviewUrls"
                :key="index"
                class="preview-item"
              >
                <img :src="url" alt="预览图片" >
                <button class="remove-image" @click="removeImage(index)">
                  <Icon name="heroicons:x-mark" />
                </button>
              </div>
            </div>
          </div>
          <button class="submit-btn">发布</button>
        </div>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.forum-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.forum-container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px var(--default-padding-x);
}

/* 筛选部分样式 */
.filter-section {
  margin-bottom: 20px;
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

/* 发帖按钮 */
.new-post-btn {
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

.new-post-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 6px 16px rgba(0, 0, 0, 0.2);
}

/* 帖子列表样式 */
.posts-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.post-card {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
  transition: transform 0.3s ease;
}

.post-card:hover {
  transform: translateY(-2px);
}

.post-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.author-info {
  display: flex;
  align-items: center;
  gap: 10px;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-weight: 500;
}

.author-name {
  font-weight: 500;
  color: #333;
}

.post-category {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #666;
  font-size: 0.9rem;
}

.post-title {
  font-size: 1.2rem;
  color: #333;
  margin: 0 0 10px;
}

.post-content {
  color: #666;
  font-size: 0.95rem;
  line-height: 1.5;
  margin: 0 0 15px;
}

.post-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 15px;
  border-top: 1px solid #eee;
}

.post-stats {
  display: flex;
  gap: 20px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #666;
  font-size: 0.9rem;
}

.last-reply {
  color: #999;
  font-size: 0.85rem;
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
}

.modal-content {
  background: white;
  padding: 30px;
  border-radius: 12px;
  position: relative;
  width: 90%;
  max-width: 600px;
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
  .forum-container {
    padding: 10px;
  }

  .filter-tabs {
    padding-bottom: 10px;
  }

  .filter-tab {
    padding: 6px 16px;
    font-size: 0.85rem;
  }

  .post-card {
    padding: 15px;
  }

  .post-title {
    font-size: 1.1rem;
  }

  .post-footer {
    flex-direction: column;
    gap: 10px;
    align-items: flex-start;
  }

  .post-stats {
    gap: 15px;
  }

  .modal-content {
    padding: 20px;
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
  margin: 0 0 20px;
}

.create-post-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 20px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.create-post-btn:hover {
  opacity: 0.9;
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

/* 帖子图片 */
.post-images {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  gap: 10px;
  margin: 15px 0;
}

.image-preview {
  aspect-ratio: 1;
  border-radius: 8px;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
}

.image-placeholder {
  color: #999;
  font-size: 1.5rem;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .image-preview-list {
    grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
  }

  .post-images {
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
