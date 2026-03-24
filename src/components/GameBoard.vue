<script setup>
defineProps({
  colors: Array,
  isPlaying: Boolean,
  gameOver: Boolean,
  handleUserClick: Function
})
</script>

<template>
  <div class="grid">
    <button
      v-for="color in colors"
      :key="color"
      :class="['color', color]"
      @click="handleUserClick(color)"
      :disabled="isPlaying || gameOver"
    ></button>
  </div>
</template>