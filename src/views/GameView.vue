<script setup>
import { ref } from 'vue'
import GameBoard from '../components/GameBoard.vue'
import ScoreBoard from '../components/ScoreBoard.vue'
import ProgressBar from '../components/ProgressBar.vue'

const props = defineProps({
    difficulty: String
})

const emit = defineEmits(['restart'])

// Game state
const sequence = ref([])
const userSequence = ref([])
const level = ref(0)
const score = ref(0)
const isPlaying = ref(false)
const gameOver = ref(false)

// Tries system
const tries = ref(3)
const maxTries = 3

const colors = ['red', 'blue', 'green', 'yellow']

const speeds = {
    easy: 1200,
    medium: 600,
    hard: 300
}

// High score
const highScore = ref(
    Number(localStorage.getItem('highScore')) || 0
)

// Start game
function startGame() {
    sequence.value = []
    userSequence.value = []
    level.value = 0
    score.value = 0
    gameOver.value = false

    tries.value = maxTries

    nextLevel()
}

startGame()

// Advance to next level
function nextLevel() {
    level.value++
    userSequence.value = []

    const randomColor = colors[Math.floor(Math.random() * colors.length)]
    sequence.value.push(randomColor)

    score.value = level.value - 1

    if (score.value > highScore.value) {
        highScore.value = score.value
        localStorage.setItem('highScore', highScore.value)
    }

    playSequence()
}

// Show sequence to user
function playSequence() {
    isPlaying.value = true

    sequence.value.forEach((color, index) => {
        setTimeout(() => {
            flashColor(color)
        }, (index + 1) * speeds[props.difficulty])
    })

    // Wait after sequence finishes before enabling input
    const totalTime = sequence.value.length * speeds[props.difficulty]

    setTimeout(() => {
        isPlaying.value = false
    }, totalTime + 600) // Delay
}

// Flash color effect
function flashColor(color) {
    const button = document.querySelector(`.${color}`)
    if (!button) return

    button.classList.add('active')

    setTimeout(() => {
        button.classList.remove('active')
    }, 250)
}

// Handle user input
function handleUserClick(color) {
    if (isPlaying.value || gameOver.value) return

    userSequence.value.push(color)

    const index = userSequence.value.length - 1

    // Check if user input is correct
    if (userSequence.value[index] !== sequence.value[index]) {
        tries.value--

        if (tries.value <= 0) {
            gameOver.value = true
            return
        }

        // Allow retry of the same sequence
        userSequence.value = []

        setTimeout(() => {
            playSequence()
        }, 1200)

        return
    }

    // Check if level is completed
    if (userSequence.value.length === sequence.value.length) {
        setTimeout(nextLevel, 1000)
    }
}
</script>

<template>
    <div class="game-view">

        <ScoreBoard :level="level" :score="score" :highScore="highScore" />

        <ProgressBar :progress="level * 10" />

        <div class="lives">
            <span v-for="n in tries" :key="n" class="heart">
                <img src="../assets/heart.png" alt="Heart icon">
            </span>
        </div>

        <GameBoard :colors="colors" :isPlaying="isPlaying" :gameOver="gameOver" :handleUserClick="handleUserClick" />

        <button v-if="gameOver" @click="$emit('restart')" class="back-menu-btn">
            Back to Menu
        </button>

    </div>
</template>