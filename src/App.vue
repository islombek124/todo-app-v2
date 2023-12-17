<script setup lang="ts">
import { RouterView } from 'vue-router'
</script>

<template>
  <main>
    <RouterView />
  </main>
</template>

<style scoped>
main {
  min-height: 100vh;
  @apply bg-slate-500/10;
}
</style>