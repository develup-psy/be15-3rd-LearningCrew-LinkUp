<script setup>
import { isLoading } from '@/composables/useLoadingBar';
</script>

<template>
  <transition name="fade">
    <div v-if="isLoading" class="global-loader">
      <div class="spinner" />
    </div>
  </transition>
</template>

<style scoped>
.global-loader {
  @apply fixed inset-0 bg-black bg-opacity-30 flex items-center justify-center z-50;
}
.spinner {
  @apply w-12 h-12 border-4 border-white border-t-transparent rounded-full animate-spin;
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
