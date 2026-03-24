<script setup>
defineProps(['level', 'score', 'highScore'])
</script>

<template>
  <div>
    <h2>Level: {{ level }}</h2>
    <h2>Score: {{ score }}</h2>
    <h2>High Score: {{ highScore }}</h2>
  </div>
</template>