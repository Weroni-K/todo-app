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

const props = defineProps({
  isDarkMode: Boolean,
})

const emit = defineEmits(['toggle-dark-mode'])

const toggleDarkMode = () => {
  emit('toggle-dark-mode')
}
</script>

<style scoped>
.navbar {
  padding: 0;
  margin: auto auto 3rem;
  width: 100%;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

h1 {
  font-size: 42px;
  color: var(--color-header);
  font-family: var(--font-body);
  font-weight: var(--font-weight-700);
  text-transform: uppercase;
  letter-spacing: 1rem;
}

.toggle-theme {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

.theme-icon {
  height: 28px;
  aspect-ratio: 1;
  cursor: pointer;
  transition:
    transform 0.3s ease,
    opacity 0.3s ease;
}

.theme-icon:hover {
  transform: scale(1.1);
  opacity: 0.8;
}

@media (max-width: 592px) {
  .navbar {
    margin-bottom: 2.5rem;
  }
  h1 {
    font-size: 24px;
  }
  .theme-icon {
    height: 22px;
  }
}
</style>
