<script setup lang="ts">
import { inject, onMounted, ref } from 'vue';
import type ThemeService from '../../services/theme.service.ts';

const themeService = inject<ThemeService>('themeService') as ThemeService;

const theme = ref<string>(themeService.getCurrentTheme());

onMounted(() => {
  themeService.theme$.asObservable().subscribe(res => {
    theme.value = res;
  });
});
</script>

<template>
  <div class="theme-switcher">
    <Transition name="fade">
      <svg v-if="theme === 'dark'" viewBox="-2 -2 20 20" class="light" @click="themeService.change('light')">
        <path
          d="M7 0h2v2H7zM12.88 1.637l1.414 1.415-1.415 1.413-1.413-1.414zM14 7h2v2h-2zM12.95 14.433l-1.414-1.413 1.413-1.415 1.415 1.414zM7 14h2v2H7zM2.98 14.364l-1.413-1.415 1.414-1.414 1.414 1.415zM0 7h2v2H0zM3.05 1.706 4.463 3.12 3.05 4.535 1.636 3.12z"
        />
        <path d="M8 4C5.8 4 4 5.8 4 8s1.8 4 4 4 4-1.8 4-4-1.8-4-4-4Z" />
      </svg>
    </Transition>
    <Transition name="fade">
      <svg v-if="theme === 'light'" viewBox="-2 -2 20 20" class="dark" @click="themeService.change('dark')">
        <path d="M6.2 1C3.2 1.8 1 4.6 1 7.9 1 11.8 4.2 15 8.1 15c3.3 0 6-2.2 6.9-5.2C9.7 11.2 4.8 6.3 6.2 1Z" />
        <path
          d="M12.5 5a.625.625 0 0 1-.625-.625 1.252 1.252 0 0 0-1.25-1.25.625.625 0 1 1 0-1.25 1.252 1.252 0 0 0 1.25-1.25.625.625 0 1 1 1.25 0c.001.69.56 1.249 1.25 1.25a.625.625 0 1 1 0 1.25c-.69.001-1.249.56-1.25 1.25A.625.625 0 0 1 12.5 5Z"
        /></svg
    ></Transition>
  </div>
</template>

<style scoped lang="scss">
.theme-switcher {
  position: relative;
  width: 20px;
  height: 20px;

  svg {
    transform: scale(1.1);
    position: absolute;
    inset: 0;

    &.dark {
      path:first-of-type {
        fill: #a3a070;
      }

      path:last-of-type {
        fill: yellow;
      }
    }

    &.light path {
      fill: white;
    }
  }
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
  animation-name: spin;
  animation-duration: 400ms;
  animation-timing-function: linear;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(359deg);
  }
}
</style>
