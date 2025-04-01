<script setup lang="ts">
import { useHead } from '#imports'
import 'assets/root.css'
useHead({
  title: '爱尔兰华人二手交易、租房转租、吃喝玩乐论坛 - IrishCN',
  meta: [
    { name: 'description', content: '爱尔兰华人二手交易、租房转租、华人交流平台，提供吃喝玩乐、美食推荐、生活攻略等信息。' },
    { name: 'keywords', content: '爱尔兰, 华人, 二手交易, 租房, 转租, 论坛, 生活, 吃喝玩乐, Chinese in Ireland' },
    { property: 'og:title', content: '爱尔兰华人交流论坛 | 二手交易 | 租房转租 | 吃喝玩乐' },
    { property: 'og:description', content: '加入IrishCN论坛，获取最新爱尔兰华人生活信息，二手交易、租房转租、美食娱乐推荐。' },
    // { property: 'og:image', content: '/logo.png' },
    // { property: 'og:url', content: 'https://www.irishcn.com' },
    { name: 'twitter:card', content: 'summary_large_image' },
  ],
  // link: [{ rel: 'icon', type: 'image/png', href: '/favicon.ico' }],
});
</script>
<template>
  <div>
    <NuxtPage />
  </div>
</template>
