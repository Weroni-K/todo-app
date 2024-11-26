<template>
  <div class="background-img" :style="{ backgroundImage: `url('${bgImage}')` }">
    <div class="content-container">
      <HeaderSection :is-dark-mode="isDarkMode" @toggle-dark-mode="toggleDarkMode" />
      <TodoSection />
    </div>
  </div>
</template>

<script setup>
import { ref, watch, computed, onMounted } from 'vue'
import HeaderSection from './components/HeaderSection.vue'
import TodoSection from './components/TodoSection.vue'
import BgDark from './assets/images/bg-desktop-dark.jpg'
import BgLight from './assets/images/bg-desktop-light.jpg'
import BgMobileDark from './assets/images/bg-mobile-dark.jpg'
import BgMobileLight from './assets/images/bg-mobile-light.jpg'

const isDarkMode = ref(false)
const windowWidth = ref(window.innerWidth)

onMounted(() => {
  window.addEventListener('resize', () => {
    windowWidth.value = window.innerWidth
  })
})

const bgImage = computed(() => {
  if (windowWidth.value < 592) {
    return isDarkMode.value ? BgMobileDark : BgMobileLight
  }
  return isDarkMode.value ? BgDark : BgLight
})

const toggleDarkMode = () => {
  isDarkMode.value = !isDarkMode.value
  document.documentElement.classList.toggle('dark-theme', isDarkMode.value)
}

if (localStorage.getItem('dark-mode') === 'true') {
  isDarkMode.value = true
  document.documentElement.classList.add('dark-theme')
}

watch(isDarkMode, (newValue) => {
  localStorage.setItem('dark-mode', newValue)
})
</script>

<style scoped>
.background-img {
  width: auto;
  max-width: 1440px;
  height: 300px;
  margin: auto;
  background: center top / cover no-repeat;
}

.content-container {
  width: 36rem;
  margin: auto;
  padding: 4.5rem 0 3rem;
}

@media (max-width: 592px) {
  .background-img {
    height: 200px;
  }
  .content-container {
    width: auto;
    padding: 3rem 24px 2rem;
  }
}
</style>
