<script setup lang="ts">
const props = defineProps({
  list: {
    type: Array,
    default: () => []
  }
})
const imageList = computed(() => props.list || [])

// 当前索引
const currentIndex = ref(0)

function selectAction(index: number) {
  currentIndex.value = index
}
const nuxtSwiper = ref<HTMLElement | null>(null)

const ulStyle = computed(() => {
  const offset = Math.max(currentIndex.value - 1, 0)
  return {
    transform: `translate(-${244 * offset}px, 0)`,
  }
})

</script>

<template>
  <div class="nuxt-swiper-wrapper">
    <div class="nuxt-swiper">
      <h2>Nuxt Swiper</h2>
      <ul ref="nuxtSwiper" class="nuxt-swiper-list" :style="ulStyle">
        <!-- 前补位 -->
        <Transition name="swiper-slide">
          <li v-show="currentIndex == 0" class="swiper-slide" @click="selectAction(imageList.length  - 1)">
            <img :src="imageList[imageList.length - 1].url" alt="" srcset="">
          </li>
        </Transition>
        <!-- 真实 -->
        <li
          v-for="(item, index) in imageList"
          :key="index"
          class="swiper-slide"
          :class="[{ 'swiper-slide-active': index == currentIndex }]"
          @click="selectAction(index)">
          <img :src="item.url" alt="" srcset="">
        </li>
        <!-- 后补位 -->
        <Transition name="swiper-slide">
          <li v-show="currentIndex > 2" class="swiper-slide" @click="selectAction(0)">
            <img :src="imageList[0].url" alt="" srcset="">
          </li>
        </Transition>
      </ul>
    </div>
    <div class="nav-area">
      {{ currentIndex + 1 }} / {{ imageList.length }}
      <ul class="nav-list">
        <li
          v-for="index in imageList.length"
          :key="index"
          :class="[{ active: currentIndex + 1 == index }]"
          @click="selectAction(index - 1)"></li>
      </ul>
    </div>
  </div>
</template>

<style lang="less" scoped>
@import url('./swiper.less');
</style>