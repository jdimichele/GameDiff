<template>
  <transition name="page" mode="out-in">
    <home-page
      v-if="page === 'home'"
      key="home"
      @submit="goToResults"
    />

    <patch-diff-page
      v-else-if="page === 'patchdiff'"
      key="results"
      :game-name="query.gameName"
      :manual-notes="query.manualNotes"
      :last-date-played="query.lastDatePlayed"
      :last-version-played="query.lastVersionPlayed"
      @go-home="goHome"
    />
  </transition>
</template>

<script setup>
import { ref } from 'vue'
import HomePage from './pages/HomePage.vue';
import PatchDiffPage from './pages/PatchDiffPage.vue';

const page = ref('home')
const query = ref({
  gameName: '',
  manualNotes: '',
  lastDatePlayed: '',
  lastVersionPlayed: '',
})

function goToResults(payload) {
  query.value = payload
  page.value = 'patchdiff'
}

function goHome() {
  page.value = 'home'
}
</script>

<style scoped>
.page-enter-active,
.page-leave-active {
  transition: opacity 0.25s ease, transform 0.25s ease;
}
.page-enter-from {
  opacity: 0;
  transform: translateY(12px);
}
.page-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}
</style>
