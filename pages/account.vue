<script setup lang="ts">
const activeTab = ref('profile')
const isMobileMenuOpen = ref(false)

const menuItems = [
  { id: 'profile', label: '个人资料', icon: 'heroicons:user-circle' },
  { id: 'posts', label: '我的发布', icon: 'heroicons:document-text' },
  { id: 'favorites', label: '我的收藏', icon: 'heroicons:heart' },
  { id: 'settings', label: '账户设置', icon: 'heroicons:cog-6-tooth' },
  { id: 'security', label: '安全设置', icon: 'heroicons:shield-check' }
]

const toggleMobileMenu = () => {
  isMobileMenuOpen.value = !isMobileMenuOpen.value
}

const handleTabChange = (tab: string) => {
  activeTab.value = tab
  if (isMobileMenuOpen.value) {
    isMobileMenuOpen.value = false
  }
}
</script>

<template>
  <div>
    <Topbar />
    <SearchBar />
    <div class="account-page">
      
    <!-- 移动端菜单按钮 -->
    <button class="mobile-menu-btn" @click="toggleMobileMenu">
      <Icon :name="isMobileMenuOpen ? 'heroicons:x-mark' : 'heroicons:bars-3'" />
    </button>

    <!-- 侧边栏 -->
    <aside class="sidebar" :class="{ 'open': isMobileMenuOpen }">
      <div class="user-info">
        <div class="avatar">
          <Icon name="heroicons:user-circle" />
        </div>
        <div class="user-details">
          <h3>用户名</h3>
          <p>user@example.com</p>
        </div>
      </div>

      <nav class="nav-menu">
        <button
          v-for="item in menuItems"
          :key="item.id"
          class="nav-item"
          :class="{ 'active': activeTab === item.id }"
          @click="handleTabChange(item.id)"
        >
          <Icon :name="item.icon" class="nav-icon" />
          <span>{{ item.label }}</span>
        </button>
      </nav>
    </aside>

    <!-- 主要内容区域 -->
    <main class="main-content">
      <div class="content-header">
        <h2>{{ menuItems.find(item => item.id === activeTab)?.label }}</h2>
      </div>

      <div class="content-body">
        <!-- 个人资料 -->
        <div v-if="activeTab === 'profile'" class="profile-section">
          <div class="profile-form">
            <div class="form-group">
              <label>头像</label>
              <div class="avatar-upload">
                <Icon name="heroicons:user-circle" class="avatar-preview" />
                <button class="upload-btn">更换头像</button>
              </div>
            </div>
            <div class="form-group">
              <label>用户名</label>
              <input type="text" placeholder="请输入用户名" class="input">
            </div>
            <div class="form-group">
              <label>邮箱</label>
              <input type="email" placeholder="请输入邮箱" class="input">
            </div>
            <div class="form-group">
              <label>个人简介</label>
              <textarea placeholder="请输入个人简介" class="textarea"/>
            </div>
            <button class="save-btn">保存修改</button>
          </div>
        </div>

        <!-- 我的发布 -->
        <div v-if="activeTab === 'posts'" class="posts-section">
          <div class="posts-grid">
            <div v-for="i in 6" :key="i" class="post-card">
              <div class="post-image"/>
              <div class="post-info">
                <h3>发布标题 {{ i }}</h3>
                <p>发布时间：2024-03-{{ i }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- 我的收藏 -->
        <div v-if="activeTab === 'favorites'" class="favorites-section">
          <div class="favorites-grid">
            <div v-for="i in 6" :key="i" class="favorite-card">
              <div class="favorite-image"/>
              <div class="favorite-info">
                <h3>收藏标题 {{ i }}</h3>
                <p>收藏时间：2024-03-{{ i }}</p>
              </div>
            </div>
          </div>
        </div>

        <!-- 账户设置 -->
        <div v-if="activeTab === 'settings'" class="settings-section">
          <div class="settings-form">
            <div class="form-group">
              <label>语言偏好</label>
              <select class="select">
                <option>简体中文</option>
                <option>English</option>
              </select>
            </div>
            <div class="form-group">
              <label>通知设置</label>
              <div class="checkbox-group">
                <label class="checkbox">
                  <input type="checkbox" checked>
                  <span>邮件通知</span>
                </label>
                <label class="checkbox">
                  <input type="checkbox" checked>
                  <span>系统通知</span>
                </label>
              </div>
            </div>
            <button class="save-btn">保存设置</button>
          </div>
        </div>

        <!-- 安全设置 -->
        <div v-if="activeTab === 'security'" class="security-section">
          <div class="security-form">
            <div class="form-group">
              <label>修改密码</label>
              <input type="password" placeholder="当前密码" class="input">
              <input type="password" placeholder="新密码" class="input">
              <input type="password" placeholder="确认新密码" class="input">
            </div>
            <div class="form-group">
              <label>两步验证</label>
              <div class="toggle-group">
                <span>启用两步验证</span>
                <button class="toggle-btn">
                  <span class="toggle-slider"/>
                </button>
              </div>
            </div>
            <button class="save-btn">保存修改</button>
          </div>
        </div>
      </div>
    </main>
  </div>
</div></template>

<style scoped>
.account-page {
  min-height: 100vh;
  display: flex;
  background-color: #f8f9fa;
}

.mobile-menu-btn {
  display: none;
  position: fixed;
  top: 20px;
  left: 20px;
  background: white;
  border: none;
  border-radius: 8px;
  padding: 8px;
  cursor: pointer;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.sidebar {
  width: 280px;
  background: white;
  padding: 20px;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.1);
  position: absolute;
  height: 100vh;
  overflow-y: auto;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 16px;
  padding-bottom: 20px;
  border-bottom: 1px solid #eee;
  margin-bottom: 20px;
}

.avatar {
  width: 60px;
  height: 60px;
  color: #666;
}

.avatar :deep(svg) {
  width: 100%;
  height: 100%;
}

.user-details h3 {
  margin: 0;
  font-size: 1.1rem;
  color: #333;
}

.user-details p {
  margin: 4px 0 0;
  font-size: 0.9rem;
  color: #666;
}

.nav-menu {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px;
  border: none;
  background: none;
  border-radius: 8px;
  cursor: pointer;
  color: #666;
  transition: all 0.3s ease;
  text-align: left;
  width: 100%;
}

.nav-item:hover {
  background-color: #f8f9fa;
  color: var(--primary-color);
}

.nav-item.active {
  background-color: var(--primary-color);
  color: white;
}

.nav-icon {
  width: 20px;
  height: 20px;
}

.main-content {
  flex: 1;
  margin-left: 280px;
  padding: 20px;
}

.content-header {
  margin-bottom: 30px;
}

.content-header h2 {
  font-size: 1.5rem;
  color: #333;
  margin: 0;
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

.input {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.input:focus {
  outline: none;
  border-color: var(--primary-color);
}

.textarea {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  min-height: 100px;
  resize: vertical;
}

.select {
  width: 100%;
  padding: 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  background-color: white;
}

.checkbox-group {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.checkbox {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
}

.toggle-group {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.toggle-btn {
  width: 50px;
  height: 26px;
  background: #ddd;
  border-radius: 13px;
  position: relative;
  cursor: pointer;
  border: none;
}

.toggle-slider {
  position: absolute;
  top: 2px;
  left: 2px;
  width: 22px;
  height: 22px;
  background: white;
  border-radius: 50%;
  transition: transform 0.3s ease;
}

.toggle-btn.active .toggle-slider {
  transform: translateX(24px);
}

.save-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.save-btn:hover {
  background-color: var(--primary-color-dark);
}

/* 卡片网格样式 */
.posts-grid,
.favorites-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}

.post-card,
.favorite-card {
  background: white;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
}

.post-card:hover,
.favorite-card:hover {
  transform: translateY(-4px);
}

.post-image,
.favorite-image {
  height: 160px;
  background-color: #eee;
}

.post-info,
.favorite-info {
  padding: 16px;
}

.post-info h3,
.favorite-info h3 {
  margin: 0 0 8px;
  font-size: 1.1rem;
  color: #333;
}

.post-info p,
.favorite-info p {
  margin: 0;
  font-size: 0.9rem;
  color: #666;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .mobile-menu-btn {
    display: block;
  }

  .sidebar {
    transform: translateX(-100%);
    transition: transform 0.3s ease;
    z-index: 99;
  }

  .sidebar.open {
    transform: translateX(0);
  }

  .main-content {
    margin-left: 0;
  }

  .posts-grid,
  .favorites-grid {
    grid-template-columns: 1fr;
  }
}

/* 动画效果 */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.content-body > div {
  animation: fadeIn 0.3s ease;
}
</style>
