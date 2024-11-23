<template>
  <div class="navbar">
    <h1>Todo</h1>
    <div class="toggle-theme">
      <img
        :src="isDarkMode ? SunIcon : MoonIcon"
        :alt="isDarkMode ? 'Light Mode' : 'Dark Mode'"
        class="theme-icon"
        @click="toggleDarkMode"
      />
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue'
import MoonIcon from '../assets/icons/icon-moon.svg'
import SunIcon from '../assets/icons/icon-sun.svg'

// const props = defineProps({
//   isDarkMode: Boolean,
// })

const emit = defineEmits(['toggle-dark-mode'])

const toggleDarkMode = () => {
  emit('toggle-dark-mode')
}
</script>

<style scoped>
.navbar {
  padding-top: 3rem;
  justify-content: center;
  padding-bottom: 2rem;
  width: 100%;
  display: flex;
  flex-direction: row;
  align-items: center;
}

.toggle-theme {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  margin-right: 16px;
}

.theme-icon {
  width: 24px;
  height: 24px;
  cursor: pointer;
  transition:
    transform 0.3s ease,
    opacity 0.3s ease;
}

.theme-icon:hover {
  transform: scale(1.1);
  opacity: 0.8;
}
</style>
