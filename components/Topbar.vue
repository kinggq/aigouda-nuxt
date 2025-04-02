<script setup lang="ts">
const showDropdown = ref(false)
</script>
<template>
  <div class="topbar">
    <div class="w-full flex justify-between items-center">
      <div class="location">
        <Icon name="heroicons:map-pin" class="location-icon" />
        都柏林
      </div>
      <div class="flex items-center gap-10">
        <div class="flex items-center gap-2">
          <div>
            <NuxtLink to="/login">登录</NuxtLink>
          </div>
          <div>|</div>
          <div>
            <NuxtLink to="/register">注册</NuxtLink>
          </div>
        </div>
        <div 
          class="user-center"
          @mouseenter="showDropdown = true"
          @mouseleave="showDropdown = false"
        >
          <span>个人中心</span>
          <Transition name="fade">
            <div v-if="showDropdown" class="dropdown-menu">
              <NuxtLink to="/account" class="dropdown-item">我的账户</NuxtLink>
              <a href="#" class="dropdown-item">退出登录</a>
            </div>
          </Transition>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.topbar {
  border-bottom: 1px solid var(--primary-border-color);
  padding: 5px var(--default-padding-x);
  font-size: .8rem;
  height: 35px;
  display: flex;
  width: 100%;
  align-items: center;
}
.location {
  color: var(--primary-color);
  font-weight: 600;
  display: flex;
  align-items: center;
  gap: 6px;
}
.location-icon {
  width: 16px;
  height: 16px;
  color: var(--primary-color);
  animation: locationPulse 2s infinite;
}
@keyframes locationPulse {
  0% {
    transform: scale(1);
    opacity: 1;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.8;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}
.topbar a {
  text-decoration: none;
  color: var(--default-color);
}
.topbar a:hover {
  color: var(--primary-color-hover);
}

.user-center {
  position: relative;
  cursor: pointer;
}

.dropdown-menu {
  position: absolute;
  top: 100%;
  right: 0;
  background: white;
  border: 1px solid var(--primary-border-color);
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  min-width: 120px;
  z-index: 1000;
}

.dropdown-item {
  display: block;
  padding: 8px 16px;
  transition: all 0.3s ease;
}

.dropdown-item:hover {
  background-color: var(--control-item-bg-hover);
  color: white;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
