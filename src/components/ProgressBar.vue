<script setup>
defineProps({
  progress: Number
})
</script>

<template>
  <div class="progress-bar">
    <div class="fill" :style="{ width: progress + '%' }"></div>
  </div>
</template>