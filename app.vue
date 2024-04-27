<script setup lang="ts">
import image01 from '~/assets/images/image01.webp'
import image02 from '~/assets/images/image02.webp'
import image03 from '~/assets/images/image03.webp'
import image04 from '~/assets/images/image04.webp'

const imageList = ref([
  { url: image01 },
  { url: image02 },
  { url: image03 },
  { url: image04 },
])
</script>

<template>
  <SwiperDemo :list="imageList" />
  <SwiperDemo2 :list="imageList" />
  <SwiperDemo3 :list="imageList" />
</template>

<style lang="less">
</style>