<script setup>
import { ref } from 'vue'
import GameView from './views/GameView.vue'

// State
const gameStarted = ref(false)
const difficulty = ref('easy')

// Start game
function startGame() {
  gameStarted.value = true
}

// Restart game
function restartGame() {
  gameStarted.value = false
}

// Set difficulty
function setDifficulty(level) {
  difficulty.value = level
}
</script>

<template>
  <GameView v-if="gameStarted" :difficulty="difficulty" @restart="restartGame" />

  <div v-else class="start-screen">
    <h1 class="app-name">Memory Trainer</h1>
    <p>Watch the pattern and repeat it</p>
    <p>Each level gets harder</p>
    <p>You have 3 tries</p>
    <h3 class="difficulty-heading">Select Difficulty</h3>
    <button @click="setDifficulty('easy')">Easy</button>
    <button @click="setDifficulty('medium')">Medium</button>
    <button @click="setDifficulty('hard')">Hard</button>

    <br />

    <button class="start-btn" @click="startGame">
      Start Game
    </button>
  </div>
</template>