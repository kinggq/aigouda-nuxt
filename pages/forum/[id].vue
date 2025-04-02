<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()
const isLoading = ref(true)

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
  createdAt: string
}

interface Comment {
  id: number
  author: {
    name: string
    avatar: string
  }
  content: string
  createdAt: string
  likes: number
  isLiked: boolean
  images?: string[]
  mentions?: string[]
  replies?: Comment[]
  isCurrentUser?: boolean
}

// 模拟帖子数据
const post = ref<Post>({
  id: 1,
  title: '都柏林租房经验分享',
  category: 'life',
  author: {
    name: '小明',
    avatar: '#FFE5E5'
  },
  content: `最近在都柏林找房，总结了一些经验分享给大家：

1. 提前准备
- 准备好护照、学生证等身份证明
- 准备3-6个月的房租作为押金
- 准备好推荐信（如果有的话）

2. 找房渠道
- Daft.ie（最常用）
- Facebook租房群
- 华人论坛
- 学校官网

3. 看房注意事项
- 检查房屋状况
- 确认是否包Bill
- 了解周边交通
- 确认合同条款

4. 签约流程
- 确认合同内容
- 支付押金和首月房租
- 办理入住手续
- 拍照记录房屋状况

希望这些经验对大家有帮助！`,
  images: ['#FFE5E5', '#E5FFE5', '#E5E5FF'],
  stats: {
    views: 1280,
    replies: 32,
    likes: 45
  },
  lastReply: {
    time: '10分钟前',
    author: '小红'
  },
  createdAt: '2024-03-15 14:30'
})

// 模拟评论数据
const comments = ref<Comment[]>([
  {
    id: 1,
    author: {
      name: '小红',
      avatar: '#FFE5E5'
    },
    content: '感谢分享！请问Daft.ie上找房大概需要提前多久开始看？',
    createdAt: '10分钟前',
    likes: 5,
    isLiked: false,
    replies: [
      {
        id: 2,
        author: {
          name: '小明',
          avatar: '#FFE5E5'
        },
        content: '建议提前2-3个月开始看，因为好房源很快就会被租掉。',
        createdAt: '5分钟前',
        likes: 3,
        isLiked: false
      }
    ]
  }
])

// 帖子状态
const isLiked = ref(false)
const isFavorited = ref(false)
const showCommentInput = ref(false)
const commentContent = ref('')
const replyTo = ref<Comment | null>(null)
const selectedImages = ref<File[]>([])
const imagePreviewUrls = ref<string[]>([])
const showUserList = ref(false)
const showImagePreview = ref(false)
const currentPreviewImage = ref('')
const uploadProgress = ref<{ [key: number]: number }>({})
const currentUser = ref({
  name: '当前用户',
  avatar: '#E5FFE5'
})
const userList = ref([
  { name: '小明', avatar: '#FFE5E5' },
  { name: '小红', avatar: '#E5FFE5' },
  { name: '小张', avatar: '#E5E5FF' }
])
const filteredUsers = ref(userList.value)
const searchQuery = ref('')
const cursorPosition = ref(0)

// 模拟加载延迟
onMounted(() => {
  setTimeout(() => {
    isLoading.value = false
  }, 1500)
})

// 返回论坛主页
const goBack = () => {
  router.push('/forum')
}

// 格式化数字
const formatNumber = (num: number) => {
  if (num >= 1000) {
    return (num / 1000).toFixed(1) + 'k'
  }
  return num.toString()
}

// 获取分类图标
const getCategoryIcon = (category: string) => {
  const icons = {
    general: 'heroicons:chat-bubble-left-right',
    life: 'heroicons:home',
    study: 'heroicons:academic-cap',
    job: 'heroicons:briefcase',
    activity: 'heroicons:user-group'
  }
  return icons[category as keyof typeof icons] || 'heroicons:square-2-stack'
}

// 获取分类标签
const getCategoryLabel = (category: string) => {
  const labels = {
    general: '综合讨论',
    life: '生活服务',
    study: '学习交流',
    job: '求职招聘',
    activity: '活动聚会'
  }
  return labels[category as keyof typeof labels] || '其他'
}

// 点赞帖子
const toggleLike = () => {
  isLiked.value = !isLiked.value
  post.value.stats.likes += isLiked.value ? 1 : -1
}

// 收藏帖子
const toggleFavorite = () => {
  isFavorited.value = !isFavorited.value
}

// 处理图片上传
const handleImageUpload = (event: Event) => {
  const input = event.target as HTMLInputElement
  if (!input.files?.length) return
  
  const files = Array.from(input.files)
  const maxImages = 9 - imagePreviewUrls.value.length
  
  if (files.length > maxImages) {
    alert(`最多只能上传${maxImages}张图片`)
    return
  }
  
  files.forEach((file, index) => {
    if (file.type.startsWith('image/')) {
      selectedImages.value.push(file)
      uploadProgress.value[index] = 0
      
      // 模拟上传进度
      const interval = setInterval(() => {
        uploadProgress.value[index] += 10
        if (uploadProgress.value[index] >= 100) {
          clearInterval(interval)
          const reader = new FileReader()
          reader.onload = (e) => {
            imagePreviewUrls.value.push(e.target?.result as string)
          }
          reader.readAsDataURL(file)
        }
      }, 200)
    } else {
      alert('请上传图片文件')
    }
  })
}

// 预览图片
const previewImage = (url: string) => {
  currentPreviewImage.value = url
  showImagePreview.value = true
}

// 关闭图片预览
const closeImagePreview = () => {
  showImagePreview.value = false
  currentPreviewImage.value = ''
}

// 移除已选择的图片
const removeImage = (index: number) => {
  selectedImages.value.splice(index, 1)
  imagePreviewUrls.value.splice(index, 1)
}

// 处理@用户搜索
const handleMentionSearch = (event: KeyboardEvent) => {
  if (event.key === '@') {
    showUserList.value = true
    searchQuery.value = ''
    cursorPosition.value = commentContent.value.length
  } else if (showUserList.value) {
    if (event.key === 'Escape') {
      showUserList.value = false
    } else if (event.key === 'Enter') {
      selectUser(userList.value[0])
    } else if (event.key === 'ArrowDown') {
      // 向下选择用户
    } else if (event.key === 'ArrowUp') {
      // 向上选择用户
    } else {
      searchQuery.value = commentContent.value.slice(cursorPosition.value)
      filteredUsers.value = userList.value.filter(user => 
        user.name.toLowerCase().includes(searchQuery.value.toLowerCase())
      )
    }
  }
}

// 选择用户
const selectUser = (user: { name: string, avatar: string }) => {
  const beforeMention = commentContent.value.slice(0, cursorPosition.value)
  const afterMention = commentContent.value.slice(cursorPosition.value)
  commentContent.value = `${beforeMention}@${user.name} ${afterMention}`
  showUserList.value = false
  searchQuery.value = ''
}

// 点赞评论
const toggleCommentLike = (comment: Comment) => {
  comment.isLiked = !comment.isLiked
  comment.likes += comment.isLiked ? 1 : -1
}

// 回复评论
const replyToComment = (comment: Comment) => {
  replyTo.value = comment
  showCommentInput.value = true
}

// 取消回复
const cancelReply = () => {
  replyTo.value = null
  showCommentInput.value = false
}

// 删除评论
const deleteComment = (comment: Comment) => {
  if (!confirm('确定要删除这条评论吗？')) return
  
  const index = comments.value.findIndex(c => c.id === comment.id)
  if (index !== -1) {
    comments.value.splice(index, 1)
  }
}

// 发表评论
const submitComment = () => {
  if (!commentContent.value.trim() && !selectedImages.value.length) return
  
  const newComment: Comment = {
    id: comments.value.length + 1,
    author: currentUser.value,
    content: commentContent.value,
    createdAt: '刚刚',
    likes: 0,
    isLiked: false,
    images: imagePreviewUrls.value,
    mentions: commentContent.value.match(/@(\w+)/g)?.map(m => m.slice(1)),
    isCurrentUser: true
  }
  
  if (replyTo.value) {
    // 回复评论
    const parentComment = comments.value.find(c => c.id === replyTo.value?.id)
    if (parentComment) {
      if (!parentComment.replies) parentComment.replies = []
      parentComment.replies.push(newComment)
    }
  } else {
    // 新评论
    comments.value.push(newComment)
  }
  
  // 重置表单
  commentContent.value = ''
  selectedImages.value = []
  imagePreviewUrls.value = []
  uploadProgress.value = {}
  showCommentInput.value = false
  replyTo.value = null
}
</script>

<template>
  <div class="forum-detail-page">
    <Topbar />
    <SearchBar />
    
    <div class="detail-container">
      <!-- 面包屑导航 -->
      <div class="breadcrumb">
        <button class="back-btn" @click="goBack">
          <Icon name="heroicons:arrow-left" />
          返回论坛
        </button>
        <div class="breadcrumb-items">
          <router-link to="/forum" class="breadcrumb-item">
            <Icon name="heroicons:home" />
            论坛
          </router-link>
          <Icon name="heroicons:chevron-right" class="separator" />
          <span class="breadcrumb-item active">
            <Icon :name="getCategoryIcon(post.category)" />
            {{ getCategoryLabel(post.category) }}
          </span>
        </div>
      </div>

      <!-- 加载状态 -->
      <div v-if="isLoading" class="loading-state">
        <div class="loading-spinner">
          <Icon name="heroicons:arrow-path" class="animate-spin" />
        </div>
        <p>加载中...</p>
      </div>

      <!-- 帖子详情 -->
      <div v-else class="post-detail">
        <!-- 帖子头部 -->
        <div class="post-header">
          <div class="author-info">
            <div class="avatar" :style="{ backgroundColor: post.author.avatar }">
              {{ post.author.name[0] }}
            </div>
            <div class="author-meta">
              <div class="author-name">{{ post.author.name }}</div>
              <div class="post-time">{{ post.createdAt }}</div>
            </div>
          </div>
          <div class="post-category">
            <Icon :name="getCategoryIcon(post.category)" />
            {{ getCategoryLabel(post.category) }}
          </div>
        </div>

        <!-- 帖子内容 -->
        <div class="post-content">
          <h1 class="post-title">{{ post.title }}</h1>
          <div class="content-text">{{ post.content }}</div>
          
          <!-- 图片展示 -->
          <div v-if="post.images?.length" class="content-images">
            <div 
              v-for="(image, index) in post.images" 
              :key="index"
              class="image-item"
              :style="{ backgroundColor: image }"
            >
              <div class="image-placeholder">
                <Icon name="heroicons:photo" />
              </div>
            </div>
          </div>
        </div>

        <!-- 帖子统计 -->
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

        <!-- 最后回复 -->
        <div class="last-reply">
          <Icon name="heroicons:chat-bubble-left" />
          <span>最后回复：{{ post.lastReply.author }} · {{ post.lastReply.time }}</span>
        </div>

        <!-- 帖子操作栏 -->
        <div class="post-actions">
          <button 
            class="action-btn"
            :class="{ active: isLiked }"
            @click="toggleLike"
          >
            <Icon name="heroicons:heart" />
            <span>{{ formatNumber(post.stats.likes) }}</span>
          </button>
          <button 
            class="action-btn"
            :class="{ active: isFavorited }"
            @click="toggleFavorite"
          >
            <Icon name="heroicons:bookmark" />
          </button>
          <button class="action-btn">
            <Icon name="heroicons:share" />
          </button>
          <button class="action-btn">
            <Icon name="heroicons:flag" />
          </button>
        </div>

        <!-- 评论区 -->
        <div class="comments-section">
          <h2 class="section-title">
            <Icon name="heroicons:chat-bubble-left-right" />
            评论 ({{ comments.length }})
          </h2>

          <!-- 评论列表 -->
          <div class="comments-list">
            <div 
              v-for="comment in comments" 
              :key="comment.id"
              class="comment-item"
            >
              <!-- 主评论 -->
              <div class="comment-main">
                <div class="comment-header">
                  <div class="comment-author">
                    <div class="avatar" :style="{ backgroundColor: comment.author.avatar }">
                      {{ comment.author.name[0] }}
                    </div>
                    <div class="author-meta">
                      <div class="author-name">{{ comment.author.name }}</div>
                      <div class="comment-time">{{ comment.createdAt }}</div>
                    </div>
                  </div>
                  <div v-if="comment.isCurrentUser" class="comment-actions">
                    <button 
                      class="action-btn small danger"
                      @click="deleteComment(comment)"
                    >
                      <Icon name="heroicons:trash" />
                      删除
                    </button>
                  </div>
                </div>
                <div class="comment-content">{{ comment.content }}</div>
                
                <!-- 评论图片 -->
                <div v-if="comment.images?.length" class="comment-images">
                  <div 
                    v-for="(image, index) in comment.images" 
                    :key="index"
                    class="image-item"
                    @click="previewImage(image)"
                  >
                    <img :src="image" alt="评论图片" >
                  </div>
                </div>
                
                <div class="comment-actions">
                  <button 
                    class="action-btn small"
                    :class="{ active: comment.isLiked }"
                    @click="toggleCommentLike(comment)"
                  >
                    <Icon name="heroicons:heart" />
                    <span>{{ comment.likes }}</span>
                  </button>
                  <button 
                    class="action-btn small"
                    @click="replyToComment(comment)"
                  >
                    <Icon name="heroicons:chat-bubble-left" />
                    回复
                  </button>
                </div>
              </div>

              <!-- 回复列表 -->
              <div v-if="comment.replies?.length" class="replies-list">
                <div 
                  v-for="reply in comment.replies" 
                  :key="reply.id"
                  class="reply-item"
                >
                  <div class="comment-author">
                    <div class="avatar" :style="{ backgroundColor: reply.author.avatar }">
                      {{ reply.author.name[0] }}
                    </div>
                    <div class="author-meta">
                      <div class="author-name">{{ reply.author.name }}</div>
                      <div class="comment-time">{{ reply.createdAt }}</div>
                    </div>
                  </div>
                  <div class="comment-content">{{ reply.content }}</div>
                  <div class="comment-actions">
                    <button 
                      class="action-btn small"
                      :class="{ active: reply.isLiked }"
                      @click="toggleCommentLike(reply)"
                    >
                      <Icon name="heroicons:heart" />
                      <span>{{ reply.likes }}</span>
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- 评论输入框 -->
          <div class="comment-input-section">
            <div v-if="showCommentInput" class="comment-input">
              <div class="input-header">
                <span v-if="replyTo" class="reply-to">
                  回复 {{ replyTo.author.name }}
                  <button class="cancel-btn" @click="cancelReply">
                    <Icon name="heroicons:x-mark" />
                  </button>
                </span>
              </div>
              <textarea
                v-model="commentContent"
                placeholder="写下你的评论... 输入@可以提及用户"
                rows="3"
                @keydown="handleMentionSearch"
              />
              
              <!-- 用户列表 -->
              <div v-if="showUserList" class="user-list">
                <div 
                  v-for="user in filteredUsers" 
                  :key="user.name"
                  class="user-item"
                  @click="selectUser(user)"
                >
                  <div class="avatar" :style="{ backgroundColor: user.avatar }">
                    {{ user.name[0] }}
                  </div>
                  <span class="user-name">{{ user.name }}</span>
                </div>
              </div>

              <!-- 图片预览 -->
              <div v-if="imagePreviewUrls.length" class="image-preview">
                <div 
                  v-for="(url, index) in imagePreviewUrls" 
                  :key="index"
                  class="preview-item"
                >
                  <div class="preview-wrapper">
                    <img :src="url" alt="预览图片" >
                    <div v-if="uploadProgress[index] < 100" class="upload-progress">
                      <div 
                        class="progress-bar"
                        :style="{ width: `${uploadProgress[index]}%` }"
                      />
                    </div>
                  </div>
                  <button class="remove-btn" @click="removeImage(index)">
                    <Icon name="heroicons:x-mark" />
                  </button>
                </div>
              </div>

              <div class="input-footer">
                <div class="upload-section">
                  <label class="upload-btn">
                    <Icon name="heroicons:photo" />
                    <input 
                      type="file" 
                      accept="image/*" 
                      multiple 
                      class="hidden"
                      @change="handleImageUpload"
                    >
                  </label>
                </div>
                <button 
                  class="submit-btn"
                  :disabled="!commentContent.trim() && !selectedImages.length"
                  @click="submitComment"
                >
                  发表评论
                </button>
              </div>
            </div>
            <button 
              v-else
              class="show-input-btn"
              @click="showCommentInput = true"
            >
              <Icon name="heroicons:pencil-square" />
              写评论
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- 图片预览 -->
    <div v-if="showImagePreview" class="image-preview-modal" @click="closeImagePreview">
      <div class="preview-content">
        <img :src="currentPreviewImage" alt="预览图片" >
        <button class="close-btn" @click="closeImagePreview">
          <Icon name="heroicons:x-mark" />
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.forum-detail-page {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.detail-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px var(--default-padding-x);
}

/* 面包屑导航 */
.breadcrumb {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 30px;
  background: white;
  padding: 15px 20px;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.back-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border: none;
  background: none;
  color: #666;
  cursor: pointer;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.back-btn:hover {
  color: var(--primary-color);
}

.breadcrumb-items {
  display: flex;
  align-items: center;
  gap: 8px;
  flex: 1;
}

.breadcrumb-item {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #666;
  text-decoration: none;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.breadcrumb-item:hover {
  color: var(--primary-color);
}

.breadcrumb-item.active {
  color: var(--primary-color);
  font-weight: 500;
}

.separator {
  color: #ddd;
  font-size: 0.8rem;
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

/* 帖子详情 */
.post-detail {
  background: white;
  border-radius: 12px;
  padding: 30px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
}

.post-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 30px;
}

.author-info {
  display: flex;
  align-items: center;
  gap: 15px;
}

.avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 1.2rem;
  font-weight: 500;
}

.author-meta {
  display: flex;
  flex-direction: column;
}

.author-name {
  font-weight: 500;
  color: #333;
  font-size: 1.1rem;
}

.post-time {
  color: #999;
  font-size: 0.9rem;
}

.post-category {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 6px 12px;
  background: #f0f0f0;
  border-radius: 20px;
  color: #666;
  font-size: 0.9rem;
}

.post-content {
  margin-bottom: 30px;
}

.post-title {
  font-size: 1.8rem;
  color: #333;
  margin: 0 0 20px;
  line-height: 1.4;
}

.content-text {
  color: #333;
  font-size: 1rem;
  line-height: 1.8;
  white-space: pre-line;
  margin-bottom: 20px;
}

.content-images {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 15px;
  margin-top: 20px;
}

.image-item {
  aspect-ratio: 1;
  border-radius: 8px;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
}

.image-placeholder {
  color: #999;
  font-size: 2rem;
}

.post-stats {
  display: flex;
  gap: 30px;
  padding: 20px 0;
  border-top: 1px solid #eee;
  border-bottom: 1px solid #eee;
  margin-bottom: 20px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  font-size: 0.95rem;
}

.last-reply {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #999;
  font-size: 0.9rem;
}

/* 帖子操作栏 */
.post-actions {
  display: flex;
  gap: 20px;
  padding: 15px 0;
  border-top: 1px solid #eee;
  border-bottom: 1px solid #eee;
  margin: 20px 0;
}

.action-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  border: none;
  background: none;
  color: #666;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.action-btn:hover {
  color: var(--primary-color);
}

.action-btn.active {
  color: var(--primary-color);
}

.action-btn.small {
  padding: 4px 8px;
  font-size: 0.9rem;
}

/* 评论区 */
.comments-section {
  margin-top: 30px;
}

.section-title {
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 1.2rem;
  color: #333;
  margin-bottom: 20px;
}

.comments-list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.comment-item {
  background: #f8f9fa;
  border-radius: 12px;
  padding: 20px;
}

.comment-main {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.comment-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 12px;
}

.comment-author {
  display: flex;
  align-items: center;
  gap: 12px;
}

.comment-content {
  color: #333;
  font-size: 0.95rem;
  line-height: 1.6;
}

.comment-actions {
  display: flex;
  gap: 15px;
}

.replies-list {
  margin-top: 15px;
  padding-left: 20px;
  border-left: 2px solid #eee;
}

.reply-item {
  margin-top: 15px;
  padding: 15px;
  background: white;
  border-radius: 8px;
}

/* 评论输入框 */
.comment-input-section {
  margin-top: 30px;
}

.comment-input {
  background: #f8f9fa;
  border-radius: 12px;
  padding: 20px;
}

.input-header {
  margin-bottom: 15px;
}

.reply-to {
  display: flex;
  align-items: center;
  gap: 8px;
  color: #666;
  font-size: 0.9rem;
}

.cancel-btn {
  padding: 2px;
  border: none;
  background: none;
  color: #999;
  cursor: pointer;
  transition: all 0.3s ease;
}

.cancel-btn:hover {
  color: #666;
}

textarea {
  width: 100%;
  padding: 12px;
  border: 1px solid #eee;
  border-radius: 8px;
  resize: vertical;
  font-size: 0.95rem;
  line-height: 1.6;
  margin-bottom: 15px;
}

textarea:focus {
  outline: none;
  border-color: var(--primary-color);
}

.input-footer {
  display: flex;
  justify-content: flex-end;
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

.submit-btn:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.submit-btn:not(:disabled):hover {
  opacity: 0.9;
}

.show-input-btn {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 12px 20px;
  border: 1px dashed #ddd;
  border-radius: 12px;
  background: none;
  color: #666;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
  width: 100%;
  justify-content: center;
}

.show-input-btn:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

/* 用户列表 */
.user-list {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  background: white;
  border: 1px solid #eee;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  z-index: 10;
  max-height: 200px;
  overflow-y: auto;
}

.user-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 8px 12px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.user-item:hover {
  background: #f8f9fa;
}

.user-name {
  font-size: 0.95rem;
  color: #333;
}

/* 图片预览 */
.image-preview {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  gap: 10px;
  margin: 15px 0;
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

.remove-btn {
  position: absolute;
  top: 4px;
  right: 4px;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: rgba(0, 0, 0, 0.5);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
}

.remove-btn:hover {
  background: rgba(0, 0, 0, 0.7);
}

/* 上传按钮 */
.upload-section {
  display: flex;
  align-items: center;
}

.upload-btn {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 16px;
  border: 1px solid #eee;
  border-radius: 20px;
  color: #666;
  cursor: pointer;
  font-size: 0.95rem;
  transition: all 0.3s ease;
}

.upload-btn:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.hidden {
  display: none;
}

/* 评论图片 */
.comment-images {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
  gap: 10px;
  margin: 12px 0;
}

.comment-images .image-item {
  aspect-ratio: 1;
  border-radius: 8px;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.3s ease;
}

.comment-images .image-item:hover {
  transform: scale(1.05);
}

.comment-images .image-item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

/* 图片预览模态框 */
.image-preview-modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  cursor: pointer;
}

.preview-content {
  position: relative;
  max-width: 90vw;
  max-height: 90vh;
}

.preview-content img {
  max-width: 100%;
  max-height: 90vh;
  object-fit: contain;
}

.close-btn {
  position: absolute;
  top: -40px;
  right: 0;
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.2);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  cursor: pointer;
  transition: all 0.3s ease;
}

.close-btn:hover {
  background: rgba(255, 255, 255, 0.3);
}

/* 上传进度 */
.preview-wrapper {
  position: relative;
  width: 100%;
  height: 100%;
}

.upload-progress {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  background: rgba(0, 0, 0, 0.5);
  padding: 4px;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.progress-bar {
  height: 2px;
  background: rgba(255, 255, 255, 0.3);
  border-radius: 1px;
  overflow: hidden;
}

.progress-fill {
  height: 100%;
  background: white;
  transition: width 0.3s ease;
}

.progress-text {
  color: white;
  font-size: 0.8rem;
  text-align: center;
}

/* 删除按钮 */
.action-btn.danger {
  color: #ff4d4f;
}

.action-btn.danger:hover {
  color: #ff7875;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .detail-container {
    padding: 10px;
  }

  .breadcrumb {
    flex-direction: column;
    align-items: flex-start;
    gap: 10px;
    padding: 15px;
  }

  .post-detail {
    padding: 20px;
  }

  .post-header {
    flex-direction: column;
    gap: 15px;
  }

  .post-category {
    align-self: flex-start;
  }

  .post-title {
    font-size: 1.5rem;
  }

  .content-images {
    grid-template-columns: 1fr;
  }

  .post-stats {
    flex-wrap: wrap;
    gap: 15px;
  }

  .post-actions {
    flex-wrap: wrap;
    gap: 10px;
  }

  .action-btn {
    padding: 6px 12px;
  }

  .comment-item {
    padding: 15px;
  }

  .replies-list {
    padding-left: 15px;
  }

  .reply-item {
    padding: 12px;
  }

  .comment-input {
    padding: 15px;
  }

  .image-preview {
    grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
    gap: 8px;
  }

  .user-list {
    max-height: 150px;
  }

  .comment-images {
    grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
    gap: 8px;
  }
  
  .preview-content {
    max-width: 95vw;
  }
}
</style>
