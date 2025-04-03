<script setup lang="ts">
import TCD from '../assets/TCD.png'
const hotItems = [
  {
    title: '都柏林大学',
    image: TCD,
    link: '/guide'
  },
  {
    title: '圣三一学院',
    image: '#E8F5E9',
    link: '/guide'
  },
  {
    title: '市中心公寓',
    image: '#F3E5F5',
    link: '/guide'
  },
  {
    title: '爱尔兰传统餐厅',
    image: '#F1F8E9',
    link: '/guide'
  },
  {
    title: '购物中心',
    image: '#E3F2FD',
    link: '/guide'
  },
  {
    title: '交通指南',
    image: '#E8F5E9',
    link: '/guide'
  }
]

const featuredItems = [
  {
    title: '留学生活',
    image: '#E3F2FD',
    link: '/guide'
  },
  {
    title: '文化体验',
    image: '#F1F8E9',
    link: '/guide'
  },
  {
    title: '美食地图',
    image: '#E3F2FD',
    link: '/guide'
  },
  {
    title: '住宿攻略',
    image: '#E8F5E9',
    link: '/guide'
  },
  {
    title: '购物推荐',
    image: '#F3E5F5',
    link: '/guide'
  },
  {
    title: '旅游景点',
    image: '#F1F8E9',
    link: '/guide'
  }
]

const scrollContainer = ref<HTMLElement | null>(null)
const scrollContainer2 = ref<HTMLElement | null>(null)

const showLeftButton = ref(false)
const showRightButton = ref(false)
const showLeftButton2 = ref(false)
const showRightButton2 = ref(false)

const scroll = (direction: 'left' | 'right', container: HTMLElement) => {
  if (direction === 'left') {
    container.scrollTo({
      left: 0,
      behavior: 'smooth'
    })
  } else {
    container.scrollTo({
      left: container.scrollWidth - container.clientWidth,
      behavior: 'smooth'
    })
  }
}

const handleWheel = (event: WheelEvent, container: HTMLElement) => {
  event.preventDefault()
  container.scrollLeft += event.deltaY
}

const checkScrollButtons = (container: HTMLElement) => {
  const { scrollLeft, scrollWidth, clientWidth } = container
  showLeftButton.value = scrollLeft > 0
  showRightButton.value = scrollLeft < scrollWidth - clientWidth - 1
  showLeftButton2.value = scrollLeft > 0
  showRightButton2.value = scrollLeft < scrollWidth - clientWidth - 1
}

const handleScroll = (container: HTMLElement) => {
  checkScrollButtons(container)
}

onMounted(() => {
  if (scrollContainer.value) {
    // scrollContainer.value.addEventListener('wheel', (e) => handleWheel(e, scrollContainer.value!))
    scrollContainer.value.addEventListener('scroll', () => handleScroll(scrollContainer.value!))
    checkScrollButtons(scrollContainer.value)
  }
  
  if (scrollContainer2.value) {
    // scrollContainer2.value.addEventListener('wheel', (e) => handleWheel(e, scrollContainer2.value!))
    scrollContainer2.value.addEventListener('scroll', () => handleScroll(scrollContainer2.value!))
    checkScrollButtons(scrollContainer2.value)
  }
})

onUnmounted(() => {
  if (scrollContainer.value) {
    scrollContainer.value.removeEventListener('wheel', (e) => handleWheel(e, scrollContainer.value!))
    scrollContainer.value.removeEventListener('scroll', () => handleScroll(scrollContainer.value!))
  }
  if (scrollContainer2.value) {
    scrollContainer2.value.removeEventListener('wheel', (e) => handleWheel(e, scrollContainer2.value!))
    scrollContainer2.value.removeEventListener('scroll', () => handleScroll(scrollContainer2.value!))
  }
})
</script>

<template>
  <div class="hot-content">
    <!-- 热门推荐 -->
    <div class="content-section">
      <h2 class="section-title">热门推荐</h2>
      <div class="scroll-container">
        <button 
          v-show="showLeftButton"
          class="scroll-button left"
          @click="scroll('left', scrollContainer!)"
        >
          <i class="arrow-left"/>
        </button>
        <div 
          ref="scrollContainer"
          class="items-container"
        >
          <NuxtLink 
            v-for="item in hotItems"
            :key="item.title"
            :to="item.link"
            class="content-item"
          >
            <div class="item-image" :style="{ backgroundColor: item.image }">
              <!-- 这里可以放置图片 -->
               <img v-if="item.image.startsWith('/')" class="item-image" :src="item.image" alt="item.title" >
            </div>
            <div class="item-title">{{ item.title }}</div>
          </NuxtLink>
        </div>
        <button 
          v-show="showRightButton"
          class="scroll-button right"
          @click="scroll('right', scrollContainer!)"
        >
          <i class="arrow-right"/>
        </button>
      </div>
    </div>

    <!-- 特色内容 -->
    <div class="content-section">
      <h2 class="section-title">特色内容</h2>
      <div class="scroll-container">
        <button 
          v-show="showLeftButton2"
          class="scroll-button left"
          @click="scroll('left', scrollContainer2!)"
        >
          <i class="arrow-left"/>
        </button>
        <div 
          ref="scrollContainer2"
          class="items-container"
        >
          <NuxtLink 
            v-for="item in featuredItems"
            :key="item.title"
            :to="item.link"
            class="content-item"
          >
            <div class="item-image" :style="{ backgroundColor: item.image }">
              <!-- 这里可以放置图片 -->
            </div>
            <div class="item-title">{{ item.title }}</div>
          </NuxtLink>
        </div>
        <button 
          v-show="showRightButton2"
          class="scroll-button right"
          @click="scroll('right', scrollContainer2!)"
        >
          <i class="arrow-right"/>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.hot-content {
  padding: 20px var(--default-padding-x);
  background: white;
}

.content-section {
  margin-bottom: 30px;
}

.section-title {
  font-size: 1.5rem;
  font-weight: 600;
  color: #333;
  margin-bottom: 20px;
}

.scroll-container {
  position: relative;
  width: 100%;
  overflow: hidden;
}

.items-container {
  display: flex;
  gap: 20px;
  overflow-x: auto;
  scroll-behavior: smooth;
  scrollbar-width: none;
  -ms-overflow-style: none;
  padding: 10px 0;
}

.items-container::-webkit-scrollbar {
  display: none;
}

.content-item {
  text-decoration: none;
  color: inherit;
  transition: transform 0.3s ease;
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 0 0 calc(16.666% - 16.67px); /* 6个item平均分配 */
  aspect-ratio: 1;
}

.content-item:hover {
  transform: translateY(-5px);
}

.item-image {
  width: 100%;
  aspect-ratio: 1;
  border-radius: 50%;
  margin-bottom: 10px;
  background-color: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
}

.content-item:hover .item-image {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.item-title {
  font-size: 0.9rem;
  text-align: center;
  color: #333;
  transition: color 0.3s ease;
}

.content-item:hover .item-title {
  color: var(--primary-color);
}

.scroll-button {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: white;
  border: 1px solid #eee;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
  transition: all 0.3s ease;
}

.scroll-button:hover {
  background: var(--primary-color);
  border-color: var(--primary-color);
}

.scroll-button:hover i {
  border-color: white;
}

.scroll-button.left {
  left: 0;
}

.scroll-button.right {
  right: 0;
}

.arrow-left,
.arrow-right {
  width: 0;
  height: 0;
  border-top: 6px solid transparent;
  border-bottom: 6px solid transparent;
  transition: border-color 0.3s ease;
}

.arrow-left {
  border-right: 6px solid #666;
}

.arrow-right {
  border-left: 6px solid #666;
}

@media (max-width: 960px) {
  .content-item {
    flex: 0 0 calc(18.1818% - 16.36px); /* 5.5个item */
  }
}

@media (max-width: 760px) {
  .content-item {
    flex: 0 0 calc(28.5714% - 17.14px); /* 3.5个item */
  }

  .section-title {
    font-size: 1.2rem;
  }
}
</style>
