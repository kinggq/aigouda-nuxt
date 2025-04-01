<script setup lang="ts">
const menuItems = [
  {
    title: '首页',
    link: '/',
    submenu: {
      categories: [
        { title: '热门推荐', link: '/hot' },
        { title: '最新上架', link: '/new' },
        { title: '特惠活动', link: '/sale' }
      ],
      image: '#F3E5F5'
    }
  },
  {
    title: '爱尔兰留学',
    link: '/study',
    submenu: {
      categories: [
        { title: '院校介绍', link: '/schools' },
        { title: '专业选择', link: '/majors' },
        { title: '申请流程', link: '/apply' }
      ],
      image: '#E3F2FD'
    }
  },
  {
    title: '生活服务',
    link: '/life',
    submenu: {
      categories: [
        { title: '住宿信息', link: '/housing' },
        { title: '交通出行', link: '/transport' },
        { title: '生活指南', link: '/guide' }
      ],
      image: '#E8F5E9'
    }
  },
  {
    title: '关于我们',
    link: '/about',
    submenu: {
      categories: [
        { title: '公司介绍', link: '/company' },
        { title: '联系方式', link: '/contact' },
        { title: '加入我们', link: '/join' }
      ],
      image: '#F1F8E9'
    }
  }
]

const activeMenu = ref<string | null>(null)
const showMore = ref(false)
const visibleMenuItems = ref(menuItems)
const moreMenuItems = ref<typeof menuItems>([])

const updateVisibleMenus = () => {
  // const containerWidth = window.innerWidth
  const menuContainer = document.querySelector('.menu-container')
  if (!menuContainer) return

  const menuWidth = menuContainer.clientWidth
  const itemWidth = 120 // 预估每个菜单项的宽度
  const moreButtonWidth = 80 // 更多按钮的宽度
  const maxItems = Math.floor((menuWidth - moreButtonWidth) / itemWidth)

  visibleMenuItems.value = menuItems.slice(0, maxItems)
  moreMenuItems.value = menuItems.slice(maxItems)
}

const handleMenuLeave = (event: MouseEvent) => {
  // 检查鼠标是否移动到下拉菜单
  const submenu = (event.currentTarget as HTMLElement).querySelector('.submenu')
  if (submenu) {
    const rect = submenu.getBoundingClientRect()
    const mouseY = event.clientY
    
    // 如果鼠标向上移动（y坐标小于菜单项底部），则隐藏下拉菜单
    if (mouseY < rect.top) {
      activeMenu.value = null
    }
  }
}

onMounted(() => {
  updateVisibleMenus()
  window.addEventListener('resize', updateVisibleMenus)
})

onUnmounted(() => {
  window.removeEventListener('resize', updateVisibleMenus)
})
</script>

<template>
  <nav class="top-navbar">
    <div class="menu-container">
      <div 
        v-for="item in visibleMenuItems" 
        :key="item.title"
        class="menu-item"
        @mouseenter="activeMenu = item.title"
        @mouseleave="handleMenuLeave"
      >
        <NuxtLink :to="item.link" class="menu-link">
          {{ item.title }}
        </NuxtLink>
        <div 
          v-if="activeMenu === item.title"
          class="submenu"
          @mouseleave="activeMenu = null"
        >
          <div class="submenu-content">
            <div class="submenu-categories">
              <NuxtLink 
                v-for="category in item.submenu.categories"
                :key="category.title"
                :to="category.link"
                class="category-link"
              >
                {{ category.title }}
              </NuxtLink>
            </div>
            <div 
              class="submenu-image"
              :style="{ backgroundColor: item.submenu.image }"
            >
              <!-- 这里可以放置图片 -->
            </div>
          </div>
        </div>
      </div>
      
      <div 
        v-if="moreMenuItems.length > 0"
        class="menu-item more-menu"
        @mouseenter="showMore = true"
        @mouseleave="showMore = false"
      >
        <span class="menu-link">更多</span>
      </div>
    </div>

    <!-- 更多按钮的下拉菜单 -->
    <div 
      v-if="showMore"
      class="more-submenu"
      @mouseenter="showMore = true"
      @mouseleave="showMore = false"
    >
      <NuxtLink 
        v-for="item in moreMenuItems"
        :key="item.title"
        :to="item.link"
        class="more-menu-item"
      >
        {{ item.title }}
      </NuxtLink>
    </div>
  </nav>
</template>

<style scoped>
.top-navbar {
  position: relative;
  background: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.menu-container {
  display: flex;
  align-items: center;
  justify-content: center;
  max-width: 1200px;
  margin: 0 auto;
  padding: 0 var(--default-padding-x);
  height: 50px;
}

.menu-item {
  padding: 0 20px;
  height: 100%;
  display: flex;
  align-items: center;
}

.menu-link {
  color: #333;
  text-decoration: none;
  font-size: 1rem;
  font-weight: 500;
  transition: color 0.3s ease;
}

.menu-link:hover {
  color: var(--primary-color);
}

.submenu {
  position: absolute;
  top: 100%;
  left: 0;
  width: 100vw;
  background: white;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.submenu-content {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  padding: 20px var(--default-padding-x);
}

.submenu-categories {
  width: 200px;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.category-link {
  color: #666;
  text-decoration: none;
  padding: 8px 0;
  transition: all 0.3s ease;
}

.category-link:hover {
  color: var(--primary-color);
  transform: translateX(5px);
}

.submenu-image {
  flex: 1;
  margin-left: 20px;
  border-radius: 4px;
  min-height: 200px;
}

.more-submenu {
  position: absolute;
  top: 100%;
  right: var(--default-padding-x);
  background: white;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  border-radius: 4px;
  min-width: 150px;
  z-index: 100;
}

.more-menu-item {
  display: block;
  padding: 12px 20px;
  color: #333;
  text-decoration: none;
  transition: all 0.3s ease;
}

.more-menu-item:hover {
  background-color: var(--primary-color-hover);
  color: white;
}

@media (max-width: 768px) {
  .menu-container {
    padding: 0 10px;
  }

  .menu-item {
    padding: 0 10px;
  }

  .submenu-content {
    flex-direction: column;
    padding: 10px;
  }

  .submenu-image {
    margin-left: 0;
    margin-top: 10px;
  }
}
</style>
