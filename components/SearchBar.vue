<script setup lang="ts">
const categories = [
  { id: 'all', name: '全部' },
  { id: 'school', name: '学校' },
  { id: 'house', name: '房源' },
  { id: 'food', name: '美食' },
  { id: 'shopping', name: '购物' }
]

const selectedCategory = ref(categories[0])
const showCategories = ref(false)
const searchQuery = ref('')
const showResults = ref(false)

// 模拟搜索结果
const searchResults = ref([
  { title: '都柏林大学', type: 'school' },
  { title: '圣三一学院', type: 'school' },
  { title: '市中心公寓', type: 'house' },
  { title: '爱尔兰传统餐厅', type: 'food' }
])

const handleSearch = () => {
  // 这里添加实际的搜索逻辑
  showResults.value = true
}

const handleInput = () => {
  if (searchQuery.value.length > 0) {
    showResults.value = true
  } else {
    showResults.value = false
  }
}

const handleCategoryLeave = (event: MouseEvent) => {
  const dropdown = (event.currentTarget as HTMLElement).querySelector('.categories-dropdown')
  if (dropdown) {
    const rect = dropdown.getBoundingClientRect()
    const mouseY = event.clientY
    
    // 如果鼠标向上移动（y坐标小于选择器底部），则隐藏下拉菜单
    if (mouseY < rect.top) {
      showCategories.value = false
    }
  }
}

const handleSearchBlur = (event: FocusEvent) => {
  // 使用 setTimeout 确保点击搜索结果的事件能够先触发
  setTimeout(() => {
    const results = (event.currentTarget as HTMLElement).closest('.search-container')?.querySelector('.search-results')
    if (results) {
      const rect = results.getBoundingClientRect()
      const mouseY = (event as MouseEvent).clientY
      
      // 如果鼠标向上移动（y坐标小于搜索框底部），则隐藏搜索结果
      if (mouseY < rect.top) {
        showResults.value = false
      }
    }
  }, 100)
}
</script>

<template>
  <div class="search-bar">
    <!-- Logo -->
    <div class="logo">
      <NuxtLink to="/" class="logo-text">爱购达</NuxtLink>
    </div>

    <!-- 搜索区域 -->
    <div class="search-container">
      <div class="search-group">
        <input 
          v-model="searchQuery"
          type="text"
          class="search-input"
          placeholder="搜索你感兴趣的内容"
          @input="handleInput"
          @focus="showResults = true"
          @blur="handleSearchBlur"
        >
        <div 
          class="category-selector" 
          @click="showCategories = !showCategories"
          @mouseleave="handleCategoryLeave"
        >
          <span>{{ selectedCategory.name }}</span>
          <i class="arrow"/>
        </div>
        <!-- 类别下拉菜单 -->
        <div 
          v-if="showCategories"
          class="categories-dropdown"
          @mouseleave="showCategories = false"
        >
          <div 
            v-for="category in categories"
            :key="category.id"
            class="category-item"
            :class="{ active: selectedCategory.id === category.id }"
            @click="selectedCategory = category; showCategories = false"
          >
            {{ category.name }}
          </div>
        </div>

        <!-- 搜索结果下拉菜单 -->
        <div 
          v-if="showResults && searchQuery"
          class="search-results"
          @mouseleave="showResults = false"
        >
          <div 
            v-for="(result, index) in searchResults"
            :key="index"
            class="result-item"
          >
            <span class="result-title">{{ result.title }}</span>
            <span class="result-type">{{ result.type }}</span>
          </div>
        </div>
      </div>
      <button class="search-button" @click="handleSearch">
        搜索
      </button>
    </div>
  </div>
</template>

<style scoped>
.search-bar {
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 20px var(--default-padding-x);
  background: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-bottom: 1px solid var(--primary-border-color);
}

.logo {
  flex-shrink: 0;
}

.logo-text {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--primary-color);
  text-decoration: none;
}

.search-container {
  position: relative;
  flex: 1;
  display: flex;
  gap: 10px;
  justify-content: center;
}

.search-group {
  position: relative;
  flex: 1;
  display: flex;
  border: 2px solid var(--primary-color);
  border-radius: 4px;
  overflow: visible;
}

.category-selector {
  display: flex;
  align-items: center;
  padding: 0 15px;
  background: white;
  cursor: pointer;
  user-select: none;
  position: relative;
  min-width: 100px;
  z-index: 101;
}

.arrow {
  width: 0;
  height: 0;
  border-left: 5px solid transparent;
  border-right: 5px solid transparent;
  border-top: 5px solid #666;
  margin-left: 5px;
}

.search-input {
  flex: 1;
  border: none;
  padding: 8px 15px;
  font-size: 1rem;
  outline: none;
}

.search-button {
  padding: 0 30px;
  background: var(--primary-color);
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  transition: all 0.3s ease;
}

.search-button:hover {
  background: var(--primary-color-hover);
}

.categories-dropdown {
  position: absolute;
  top: calc(100% + 2px);
  right: 0;
  background: white;
  border: 1px solid #eee;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  z-index: 100;
  min-width: 120px;
}

.category-item {
  padding: 10px 15px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.category-item:hover {
  background: #f5f5f5;
}

.category-item.active {
  color: var(--primary-color);
  font-weight: 500;
}

.search-results {
  position: absolute;
  top: calc(100% + 2px);
  left: 0;
  right: 0;
  background: white;
  border: 1px solid #eee;
  border-radius: 4px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  z-index: 100;
}

.result-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 15px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.result-item:hover {
  background: #f5f5f5;
}

.result-title {
  color: #333;
}

.result-type {
  color: #999;
  font-size: 0.9rem;
}

@media (max-width: 768px) {
  .search-bar {
    flex-direction: column;
    padding: 15px;
  }

  .search-container {
    width: 100%;
  }

  .category-selector {
    min-width: 80px;
  }
}
</style>
