<script setup lang="ts">
const menuItems = [
  { title: '关于我们', link: '/about' },
  { title: '联系我们', link: '/contact' },
  { title: '帮助中心', link: '/help' },
  { title: '隐私政策', link: '/privacy' },
  { title: '服务条款', link: '/terms' }
]
</script>

<template>
  <footer class="footer">
    <div class="footer-content">
      <div class="footer-menu">
        <NuxtLink 
          v-for="item in menuItems"
          :key="item.title"
          :to="item.link"
          class="menu-item"
        >
          {{ item.title }}
        </NuxtLink>
      </div>
      <div class="copyright">
        © 2024 爱尔兰生活指南. All rights reserved.
      </div>
    </div>
  </footer>
</template>

<style scoped>
.footer {
  background-color: #f8f9fa;
  padding: 40px 0;
}

.footer-content {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 var(--default-padding-x);
  text-align: center;
}

.footer-menu {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
  margin-bottom: 20px;
}

.menu-item {
  color: #666;
  text-decoration: none;
  font-size: 0.9rem;
  transition: color 0.3s ease;
}

.menu-item:hover {
  color: var(--primary-color);
}

.copyright {
  color: #999;
  font-size: 0.8rem;
}

@media (max-width: 768px) {
  .footer {
    padding: 30px 0;
  }

  .footer-menu {
    gap: 15px;
  }

  .menu-item {
    font-size: 0.85rem;
  }
}
</style>
