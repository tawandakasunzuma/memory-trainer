<script setup>
import { ref } from 'vue'
import GameView from './views/GameView.vue'

const gameStarted = ref(false) // true when game is running
const difficulty = ref('easy') // selected difficulty level

// Start game
function startGame() {
  gameStarted.value = true
}

// Restart game
function restartGame() {
  gameStarted.value = false
}

// Change difficulty
function setDifficulty(level) {
  difficulty.value = level
}
</script>

<template>
  <!-- Show GameView when game has started -->
  <GameView v-if="gameStarted" :difficulty="difficulty" @restart="restartGame" />

  <!-- Start screen before game starts -->
  <div v-else class="start-screen">
    <img src="../public/logo.png" alt="App logo" class="logo">
    <h1 class="app-name">Memory Trainer</h1>

    <!-- Game instructions -->
    <div class="app-description">
      <p>Watch the pattern and repeat it</p>
      <p>Each level gets harder</p>
      <p>You have 3 tries</p>
    </div>

    <h3 class="difficulty-heading">Select Difficulty</h3>

    <!-- Difficulty buttons -->
    <button @click="setDifficulty('easy')" :class="['easy', { selected: difficulty === 'easy' }]">
      Easy
    </button>

    <button @click="setDifficulty('medium')" :class="['medium', { selected: difficulty === 'medium' }]">
      Medium
    </button>

    <button @click="setDifficulty('hard')" :class="['hard', { selected: difficulty === 'hard' }]">
      Hard
    </button>

    <!-- Start game button -->
    <button class="start-btn" @click="startGame">
      Start Game
    </button>
  </div>
</template>