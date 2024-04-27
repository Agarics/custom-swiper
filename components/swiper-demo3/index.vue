<script setup lang="ts">
const props = defineProps({
  list: {
    type: Array,
    default: () => []
  }
})
const imageList = computed(() => props.list || [])

const preViews = 4

const direction = ref('next')

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
  direction.value = 'next'
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
  direction.value = 'prev'
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

function navClick(page: number) {
  if(page == currentPage.value) return
  const direction = page > currentPage.value ? 'next' : 'prev'
  const offset = Math.abs(page - currentPage.value)
  if(offset == 3) {
    direction == 'next' ? prevAction() : nextAction()
  } else if (offset == 1) {
    direction == 'next' ? nextAction() : prevAction()
  } else {
    new Array(offset).fill(0).forEach(() => {
      direction == 'next' ? nextAction() : prevAction()
    })
  }
}

function cardClick(page: number, index: number) {
  if(page == currentPage.value) return
  const direction = index < currentIndex.value ? 'prev' : 'next'
  const offset = Math.abs(page - currentPage.value)
  if(offset == 3) {
    direction == 'next' ? nextAction() : prevAction()
  } else if (offset == 1) {
    direction == 'next' ? nextAction() : prevAction()
  } else {
    new Array(offset).fill(0).forEach(() => {
      direction == 'next' ? nextAction() : prevAction()
    })
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

const hiddenIndexArr = computed(() => {
  let list: any = []
  if(direction.value == 'next') {
    switch(currentPage.value) {
      case 1:
        list = [0, 1, 2, 7]
        break
      case 2:
        list = [0, 1, 2, 7, 8]
        break
      case 3:
        list = [0, 1, 2, 3, 8, 9, 10, 11]
        break
      case 4:
        list = [0, 1, 2, 3, 4, 9, 10, 11]
        break
    }
    if(currentIndex.value == 8 && currentPage.value == 1) {
      list = [0, 1, 2, 3, 4, 5, 10, 11]
    }
  } else {
    switch(currentPage.value) {
      case 1:
      list = [1, 2, 7, 8, 9, 10, 11]
        break
      case 2:
        list = [1, 2, 3, 8, 9, 10, 11]
        break
      case 3:
        list = [1, 2, 3, 4, 9, 10,  11]
        break
      case 4:
        break
    }
    // 4 7 、 1 8
    if(currentIndex.value == 7 && currentPage.value == 4) {
      list = [0, 1, 2, 3, 4, 5, 10,  11]
    }
  }

  return list
})

function liStyle(index: number) {
  return {
    transition: transition.value == 'none' ? 'none' : 'width 0.5s linear',
    opacity: hiddenIndexArr.value.includes(index) ? 0 : 1,
    pointerEvents: hiddenIndexArr.value.includes(index) ? 'none' : undefined
  }
}

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
          :style="liStyle(index)"
          :class="[{ 'swiper-slide-active': index == currentIndex }]"
          @click="cardClick(imageList.findIndex(ele => ele.url == item.url) + 1, index)">
          <img :src="item.url" alt="" srcset="">
          <span>{{ imageList.findIndex(ele => ele.url == item.url) + 1 }}</span>
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
          @click="navClick(index)"></li>
      </ul>
      <button @click="prevAction">上一个</button>
      <button @click="nextAction">下一个</button>
    </div>
  </div>
</template>

<style lang="less" scoped>
@import url('./swiper.less');
</style>