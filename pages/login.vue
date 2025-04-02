<script setup lang="ts">
const showTooltip = ref(false)
const email = ref('')
const isLoading = ref(false)
const error = ref('')
const isError = ref(false)

const toggleTooltip = () => {
  showTooltip.value = !showTooltip.value
}

const closeTooltip = () => {
  showTooltip.value = false
}

const handleSubmit = async (e: Event) => {
  e.preventDefault()
  error.value = ''
  isError.value = false
  isLoading.value = true

  try {
    // 模拟API调用
    await new Promise(resolve => setTimeout(resolve, 1500))
    
    // 模拟验证逻辑
    if (!email.value) {
      throw new Error('请输入邮箱地址')
    }
    
    if (!email.value.includes('@')) {
      throw new Error('请输入有效的邮箱地址')
    }

    // 模拟登录成功
    if (email.value === 'test@example.com') {
      // 登录成功处理
      console.log('登录成功')
    } else {
      throw new Error('邮箱或密码错误')
    }
  } catch (err) {
    error.value = err instanceof Error ? err.message : '登录失败，请重试'
    isError.value = true
  } finally {
    isLoading.value = false
  }
}

// 点击外部关闭tooltip
onMounted(() => {
  document.addEventListener('click', (e) => {
    const target = e.target as HTMLElement
    if (!target.closest('.tooltip-container') && !target.closest('.info-icon')) {
      showTooltip.value = false
    }
  })
})

onUnmounted(() => {
  document.removeEventListener('click', () => {})
})
</script>

<template>
  <div class="login-page">
    <div class="logo">
      <NuxtLink to="/">爱购达</NuxtLink>
    </div>

    <div class="login-container">
      <h1 class="title">登录您的账户</h1>
      <p class="subtitle">首次使用爱购达？<NuxtLink to="/register">创建账户</NuxtLink></p>

      <form class="login-form" @submit="handleSubmit">
        <div class="form-group">
          <input 
            v-model="email"
            type="email" 
            placeholder="电子邮件或用户名"
            class="email-input"
            :class="{ 'error': isError }"
          >
          <div v-if="error" class="error-message">
            <Icon 
              :name="error.includes('邮箱') ? 'heroicons:exclamation-circle' : 'heroicons:exclamation-triangle'" 
              class="error-icon"
              :class="{ 'warning': error.includes('邮箱') }"
            />
            {{ error }}
          </div>
        </div>

        <button 
          type="submit" 
          class="continue-btn"
          :disabled="isLoading"
        >
          <Icon 
            v-if="isLoading" 
            name="heroicons:arrow-path" 
            class="loading-icon"
          />
          {{ isLoading ? '登录中...' : '继续' }}
        </button>

        <div class="divider">
          <span>或者</span>
        </div>

        <button type="button" class="social-btn google">
          <Icon name="logos:google-icon" class="social-icon" />
          <span>继续使用Google</span>
        </button>

        <button type="button" class="social-btn facebook">
          <Icon name="logos:facebook" class="social-icon" />
          <span>继续使用Facebook</span>
        </button>

        <button type="button" class="social-btn apple">
          <Icon name="logos:apple" class="social-icon" />
          <span>继续使用Apple</span>
        </button>

        <div class="remember-me">
          <label class="checkbox-container">
            <input type="checkbox">
            <span class="checkmark"/>
            保持登录状态
          </label>
          <div class="tooltip-container">
            <Icon 
              name="heroicons:information-circle" 
              class="info-icon"
              @click.stop="toggleTooltip"
            />
            <div v-if="showTooltip" class="tooltip">
              <button class="close-btn" @click="closeTooltip">
                <Icon name="heroicons:x-mark" />
              </button>
              <p>为了您的账户安全，建议在公共设备上不要勾选此选项。</p>
            </div>
          </div>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
.login-page {
  min-height: 100vh;
  padding: 20px;
  background-color: #f8f9fa;
}

.logo {
  padding: 20px;
}

.logo a {
  font-size: 1.5rem;
  font-weight: bold;
  color: var(--primary-color);
  text-decoration: none;
}

.login-container {
  max-width: 400px;
  margin: 0 auto;
  padding: 40px;
  background: white;
  border-radius: 12px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.title {
  font-size: 1.8rem;
  font-weight: 600;
  color: #333;
  margin-bottom: 8px;
  text-align: center;
}

.subtitle {
  text-align: center;
  color: #666;
  margin-bottom: 30px;
}

.subtitle a {
  color: var(--primary-color);
  text-decoration: none;
}

.login-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.form-group {
  width: 100%;
}

.email-input {
  width: 100%;
  padding: 12px 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.email-input:focus {
  outline: none;
  border-color: var(--primary-color);
}

.continue-btn,
.social-btn {
  width: 100%;
  padding: 12px;
  border-radius: 8px;
  font-size: 1rem;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.continue-btn {
  background-color: var(--primary-color);
  color: white;
  border: none;
}

.continue-btn:hover {
  background-color: var(--primary-color-dark);
}

.social-btn {
  background-color: white;
  border: 1px solid #ddd;
  color: #333;
  position: relative;
}

.social-btn:hover {
  background-color: #f8f9fa;
}

.social-icon {
  position: absolute;
  left: 10px;
  width: 20px;
  height: 20px;
}

.divider {
  display: flex;
  align-items: center;
  text-align: center;
  margin: 20px 0;
}

.divider::before,
.divider::after {
  content: '';
  flex: 1;
  border-bottom: 1px solid #ddd;
}

.divider span {
  padding: 0 10px;
  color: #666;
  font-size: 0.9rem;
}

.remember-me {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-top: 16px;
  justify-content: center;
}

.checkbox-container {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  user-select: none;
}

.checkbox-container input {
  display: none;
}

.checkmark {
  width: 18px;
  height: 18px;
  border: 1px solid #ddd;
  border-radius: 50%;
  display: inline-block;
  position: relative;
}

.checkbox-container input:checked + .checkmark::after {
  content: '';
  position: absolute;
  left: 5px;
  top: 2px;
  width: 4px;
  height: 8px;
  border: solid var(--primary-color);
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
}

.tooltip-container {
  position: relative;
  height: 20px;
}

.info-icon {
  width: 20px;
  height: 20px;
  color: #666;
  cursor: pointer;
}

.tooltip {
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%);
  background: white;
  padding: 12px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  width: 200px;
  margin-bottom: 8px;
  z-index: 100;
}

.tooltip::before {
  content: '';
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: 6px solid white;
}

.close-btn {
  position: absolute;
  top: 4px;
  right: 4px;
  background: none;
  border: none;
  cursor: pointer;
  color: #666;
  padding: 4px;
}

.close-btn:hover {
  color: #333;
}

.email-input.error {
  border-color: #dc3545;
}

.error-message {
  display: flex;
  align-items: center;
  gap: 6px;
  margin-top: 8px;
  font-size: 0.875rem;
  color: #dc3545;
}

.error-icon {
  width: 16px;
  height: 16px;
}

.error-icon.warning {
  color: #ffc107;
}

.continue-btn {
  position: relative;
}

.continue-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.loading-icon {
  width: 18px;
  height: 18px;
  animation: spin 1s linear infinite;
  margin-right: 8px;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

@media (max-width: 600px) {
  .logo {
    text-align: center;
  }

  .login-container {
    padding: 20px;
  }

  .title {
    font-size: 1.5rem;
  }
}
</style>
