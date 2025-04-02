<script setup lang="ts">
const name = ref('')
const email = ref('')
const password = ref('')
const showPassword = ref(false)
const isLoading = ref(false)
const errors = ref({
  name: '',
  email: '',
  password: ''
})
const isError = ref(false)

const validateField = (field: string, value: string) => {
  switch (field) {
    case 'name':
      if (!value) {
        errors.value.name = '请输入姓名'
        return false
      }
      errors.value.name = ''
      return true
    case 'email':
      if (!value) {
        errors.value.email = '请输入邮箱地址'
        return false
      }
      if (!value.includes('@')) {
        errors.value.email = '请输入有效的邮箱地址'
        return false
      }
      errors.value.email = ''
      return true
    case 'password':
      if (!value) {
        errors.value.password = '请输入密码'
        return false
      }
      if (value.length < 6) {
        errors.value.password = '密码长度至少为6位'
        return false
      }
      errors.value.password = ''
      return true
    default:
      return true
  }
}

const handleBlur = (field: string) => {
  validateField(field, field === 'name' ? name.value : field === 'email' ? email.value : password.value)
}

const handleSubmit = async (e: Event) => {
  e.preventDefault()
  isError.value = false
  errors.value = { name: '', email: '', password: '' }

  // 验证所有字段
  const isNameValid = validateField('name', name.value)
  const isEmailValid = validateField('email', email.value)
  const isPasswordValid = validateField('password', password.value)

  if (!isNameValid || !isEmailValid || !isPasswordValid) {
    return
  }

  isLoading.value = true

  try {
    // 模拟API调用
    await new Promise(resolve => setTimeout(resolve, 1500))
    
    // 模拟注册失败情况
    if (email.value === 'test@example.com') {
      throw new Error('该邮箱已被注册')
    }

    // 注册成功处理
    console.log('注册成功')
  } catch (err) {
    isError.value = true
    errors.value.email = err instanceof Error ? err.message : '注册失败，请重试'
  } finally {
    isLoading.value = false
  }
}
</script>

<template>
  <div class="register-page">
    <div class="header">
      <div class="logo">
        <NuxtLink to="/">爱购达</NuxtLink>
      </div>
      <div class="login-link">
        已有账号？<NuxtLink to="/login">登录</NuxtLink>
      </div>
    </div>

    <div class="register-container">
      <h1 class="title">创建一个账号</h1>

      <form class="register-form" @submit="handleSubmit">
        <div class="form-group">
          <input 
            v-model="name"
            type="text" 
            placeholder="请输入姓名"
            class="input"
            :class="{ 'error': errors.name }"
            @blur="handleBlur('name')"
          >
          <div v-if="errors.name" class="error-message">
            <Icon name="heroicons:exclamation-circle" class="error-icon warning" />
            {{ errors.name }}
          </div>
        </div>

        <div class="form-group">
          <input 
            v-model="email"
            type="email" 
            placeholder="请输入邮箱"
            class="input"
            :class="{ 'error': errors.email }"
            @blur="handleBlur('email')"
          >
          <div v-if="errors.email" class="error-message">
            <Icon 
              :name="errors.email.includes('邮箱') ? 'heroicons:exclamation-circle' : 'heroicons:exclamation-triangle'" 
              class="error-icon"
              :class="{ 'warning': errors.email.includes('邮箱') }"
            />
            {{ errors.email }}
          </div>
        </div>

        <div class="form-group">
          <div class="password-input">
            <input 
              v-model="password"
              :type="showPassword ? 'text' : 'password'"
              placeholder="请输入密码"
              class="input"
              :class="{ 'error': errors.password }"
              @blur="handleBlur('password')"
            >
            <button 
              type="button" 
              class="toggle-password"
              @click="showPassword = !showPassword"
            >
              <Icon 
                :name="showPassword ? 'heroicons:eye-slash' : 'heroicons:eye'" 
                class="eye-icon"
              />
            </button>
          </div>
          <div v-if="errors.password" class="error-message">
            <Icon name="heroicons:exclamation-circle" class="error-icon warning" />
            {{ errors.password }}
          </div>
        </div>

        <div class="terms">
          <p>通过选择创建个人帐户，您同意我们的<NuxtLink to="/terms">用户协议</NuxtLink>并确认已阅读我们的<NuxtLink to="/privacy">用户隐私声明</NuxtLink>。</p>
        </div>

        <button 
          type="submit" 
          class="submit-btn"
          :disabled="isLoading"
        >
          <Icon 
            v-if="isLoading" 
            name="heroicons:arrow-path" 
            class="loading-icon"
          />
          {{ isLoading ? '创建中...' : '创建个人账户' }}
        </button>

        <div class="divider">
          <span>或继续</span>
        </div>

        <div class="social-buttons">
          <button type="button" class="social-btn google">
            <Icon name="logos:google-icon" class="social-icon" />
            <span>Google</span>
          </button>

          <button type="button" class="social-btn facebook">
            <Icon name="logos:facebook" class="social-icon" />
            <span>Facebook</span>
          </button>

          <button type="button" class="social-btn apple">
            <Icon name="logos:apple" class="social-icon" />
            <span>Apple</span>
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<style scoped>
.register-page {
  min-height: 100vh;
  padding: 20px;
  background-color: #f8f9fa;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  margin-bottom: 20px;
}

.logo a {
  font-size: 1.5rem;
  font-weight: bold;
  color: var(--primary-color);
  text-decoration: none;
}

.login-link {
  color: #666;
}

.login-link a {
  color: var(--primary-color);
  text-decoration: none;
}

.register-container {
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
  margin-bottom: 30px;
  text-align: center;
}

.register-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.form-group {
  width: 100%;
}

.input {
  width: 100%;
  padding: 12px 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
}

.input:focus {
  outline: none;
  border-color: var(--primary-color);
}

.input.error {
  border-color: #dc3545;
}

.password-input {
  position: relative;
}

.toggle-password {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
  padding: 4px;
  color: #666;
}

.eye-icon {
  width: 20px;
  height: 20px;
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

.terms {
  font-size: 0.875rem;
  color: #666;
  text-align: center;
  margin: 16px 0;
}

.terms a {
  color: var(--primary-color);
  text-decoration: none;
}

.submit-btn {
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
  background-color: var(--primary-color);
  color: white;
  border: none;
}

.submit-btn:hover {
  background-color: var(--primary-color-dark);
}

.submit-btn:disabled {
  opacity: 0.7;
  cursor: not-allowed;
}

.loading-icon {
  width: 18px;
  height: 18px;
  animation: spin 1s linear infinite;
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

.social-buttons {
  display: flex;
  gap: 12px;
}

.social-btn {
  flex: 1;
  padding: 12px;
  border-radius: 8px;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  background-color: white;
  border: 1px solid #ddd;
  color: #333;
}

.social-btn:hover {
  background-color: #f8f9fa;
}

.social-icon {
  width: 20px;
  height: 20px;
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
  .header {
    flex-direction: column;
    gap: 10px;
    text-align: center;
  }

  .register-container {
    padding: 20px;
  }

  .title {
    font-size: 1.5rem;
  }

  .social-buttons {
    flex-direction: column;
  }
}
</style>
