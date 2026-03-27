<script setup>
defineProps({
  level: Number, // game level
  score: Number, // current score
  highScore: Number // high score
})
</script>

<template>
  <div class="scoreboard">
    <h2>Level: {{ level }}</h2>
    <h2>Score: {{ score }}</h2>
    <h2>High Score: {{ highScore }}</h2>
  </div>
</template>