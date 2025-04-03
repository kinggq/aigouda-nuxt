<script setup lang="ts">
import { Swiper, SwiperSlide } from 'swiper/vue'
import { Autoplay, Navigation, Pagination } from 'swiper/modules'
import 'swiper/css'
import 'swiper/css/navigation'
import 'swiper/css/pagination'
import Irland1 from '../assets/Irland1.jpg'
import Irland2 from '../assets/Irland2.jpg'
const slides = [
  {
    title: '探索爱尔兰',
    subtitle: '发现翡翠岛的无限魅力',
    color: '#E8F5E9',
    image: Irland1,
    link: '/business'
  },
  {
    title: '文化体验',
    subtitle: '感受独特的爱尔兰文化',
    color: '#F3E5F5',
    image: Irland2,
    link: '/business'
  },
  // {
  //   title: '美食之旅',
  //   subtitle: '品味地道爱尔兰美食',
  //   color: '#E3F2FD',
  //   link: '/business'
  // },
  // {
  //   title: '自然风光',
  //   subtitle: '探索壮丽的自然景观',
  //   color: '#F1F8E9',
  //   link: '/business'
  // }
]
</script>

<template>
  <div class="banner-container">
    <Swiper
      :modules="[Autoplay, Navigation, Pagination]"
      :slides-per-view="1"
      :loop="true"
      :autoplay="{
        delay: 5000,
        disableOnInteraction: false
      }"
      :pagination="{ clickable: true }"
      :navigation="true"
      class="banner-swiper"
    >
      <SwiperSlide v-for="(slide, index) in slides" :key="index">
        <div class="slide-content" :style="{ background: `url(${slide.image}) no-repeat center center`, backgroundSize: 'cover' }">
          <div class="slide-text">
            <h2 class="slide-title">{{ slide.title }}</h2>
            <p class="slide-subtitle">{{ slide.subtitle }}</p>
            <NuxtLink :to="slide.link" class="learn-more">
              获取广告位
            </NuxtLink>
          </div>
        </div>
      </SwiperSlide>
    </Swiper>
  </div>
</template>

<style scoped>
.banner-container {
  width: 100%;
  position: relative;
}

.banner-swiper {
  width: 100%;
  height: 360px;
}

.slide-content {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  padding: 0 var(--default-padding-x);
}

.slide-text {
  max-width: 500px;
  z-index: 1;
}

.slide-title {
  font-size: 2.5rem;
  font-weight: 700;
  color: #FFF;
  margin-bottom: 1rem;
}

.slide-subtitle {
  font-size: 1.2rem;
  color: #FFF;
  margin-bottom: 2rem;
}

.learn-more {
  display: inline-block;
  padding: 0.8rem 2rem;
  background-color: var(--primary-color);
  color: white;
  text-decoration: none;
  border-radius: 4px;
  transition: all 0.3s ease;
}

.learn-more:hover {
  background-color: var(--primary-color-hover);
  transform: translateY(-2px);
}

:deep(.swiper-pagination) {
  bottom: 20px !important;
}

:deep(.swiper-pagination-bullet) {
  width: 10px;
  height: 10px;
  background: rgba(255, 255, 255, 0.5);
  opacity: 1;
}

:deep(.swiper-pagination-bullet-active) {
  background: white;
}

:deep(.swiper-button-next),
:deep(.swiper-button-prev) {
  width: 40px;
  height: 40px;
  background: #ffffffe6;
  border-radius: 50%;
  color: #333;
  transition: all 0.3s ease;
  bottom: 20px;
  top: auto;
}

:deep(.swiper-button-next) {
  right: 20px;
}

:deep(.swiper-button-prev) {
  right: 80px;
  left: auto;
}

:deep(.swiper-button-prev:after),
:deep(.swiper-button-next:after) {
  font-size: 16px;
  color: #000;
}

:deep(.swiper-button-next:hover),
:deep(.swiper-button-prev:hover) {
  background: white;
}

@media (max-width: 768px) {
  .banner-swiper {
    height: auto;
    aspect-ratio: 16/9;
  }

  .slide-title {
    font-size: 2rem;
  }

  .slide-subtitle {
    font-size: 1rem;
  }
}
</style>
