<script setup lang="ts">
const props = defineProps({
  list: {
    type: Array,
    default: () => []
  }
})
const imageList = computed(() => props.list || [])

const preViews = 4

const copyImageList = imageList.value.slice(-preViews).concat(imageList.value, imageList.value.slice(0, preViews))

// 当前索引
const currentIndex = ref(preViews)

function selectAction(index: number) {
  transition.value = 'all 0.5s linear'
  currentIndex.value = index
}
const nuxtSwiper = ref<HTMLElement | null>(null)
const transition = ref('all 0.5s linear')
async function nextAction() {
  if(currentIndex.value >= copyImageList.length - preViews) {
    transition.value = 'none'
    currentIndex.value = preViews
    await nextTick()
    nuxtSwiper.value?.clientHeight
    selectAction(currentIndex.value + 1)
  } else {
    selectAction(currentIndex.value + 1)
  }
}

async function prevAction() {
  if(currentIndex.value == preViews) {
    transition.value = 'none'
    currentIndex.value = copyImageList.length - preViews
    await nextTick()
    nuxtSwiper.value?.clientHeight
    selectAction(currentIndex.value - 1)
  } else {
    selectAction(currentIndex.value - 1)
  }
}

const currentPage =  computed(() => {
  if(currentIndex.value - preViews  < imageList.value.length) {
    return currentIndex.value - preViews + 1
  } else {
    return 1
  }
})

const ulStyle = computed(() => {
  const offset = currentIndex.value - 1
  return {
    transform: `translate(-${244 * offset}px, 0)`,
    transition: transition.value
  }
})

const liStyle = computed(() => ({
  transition: transition.value
}))

</script>

<template>
  <div class="nuxt-swiper-wrapper">
    <div class="nuxt-swiper">
      <h2>Nuxt Swiper</h2>
      <ul ref="nuxtSwiper" class="nuxt-swiper-list" :style="ulStyle">
        <li
          v-for="(item, index) in copyImageList"
          :key="index"
          class="swiper-slide"
          :style="liStyle"
          :class="[{ 'swiper-slide-active': index == currentIndex }]">
          <img :src="item.url" alt="" srcset="">
        </li>
      </ul>
    </div>
    <div class="nav-area">
      {{ currentPage }} / {{ imageList.length }}
      <ul class="nav-list">
        <li
          v-for="index in imageList.length"
          :key="index"
          :class="[{ active: currentPage == index }]"
          @click="selectAction(index + preViews - 1)"></li>
      </ul>
      <button @click="prevAction">上一个</button>
      <button @click="nextAction">下一个</button>
    </div>
  </div>
</template>

<style lang="less" scoped>
@import url('./swiper.less');
</style>