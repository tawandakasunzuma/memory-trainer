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
const activeColor = ref(null)

// Tries system
const tries = ref(3)
const maxTries = 3

const colors = ['red', 'blue', 'green', 'yellow']

const speeds = {
    easy: 600,
    medium: 300,
    hard: 150
}

// High score (per difficulty)
function getHighScoreKey() {
    return `highScore_${props.difficulty}`
}

const highScore = ref(
    Number(localStorage.getItem(getHighScoreKey())) || 0
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

// Add a delay before starting the first level
setTimeout(() => {
    nextLevel()
}, 150)

// Advance to next level
function nextLevel() {
    level.value++
    userSequence.value = []

    const randomColor = colors[Math.floor(Math.random() * colors.length)]
    sequence.value.push(randomColor)

    playSequence()
}

// Show sequence to user
function playSequence() {
    isPlaying.value = true

    const speed = speeds[props.difficulty]
    const gap = speeds[props.difficulty] * 0.6 // space between flashes

    sequence.value.forEach((color, index) => {
        setTimeout(() => {
            flashColor(color)
        }, index * (speed + gap))
    })

    // Total time including gaps
    const totalTime = sequence.value.length * (speed + gap)

    // Small pause after sequence ends before user can play
    setTimeout(() => {
        isPlaying.value = false
    }, totalTime + 600)
}

// Flash color effect
function flashColor(color) {
    activeColor.value = color

    const button = document.querySelector(`.${color}`)
    if (!button) return

    button.classList.add('active')

    const flashTime = Math.max(150, speeds[props.difficulty] * 0.6)

    setTimeout(() => {
        button.classList.remove('active')
        activeColor.value = null
    }, flashTime)
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

    // Check if level is complete
    if (userSequence.value.length === sequence.value.length) {
        score.value = level.value

        // Update high score per difficulty
        const key = getHighScoreKey()

        if (score.value > highScore.value) {
            highScore.value = score.value
            localStorage.setItem(key, score.value)
        }

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

        <GameBoard :colors="colors" :isPlaying="isPlaying" :gameOver="gameOver" :handleUserClick="handleUserClick"
            :activeColor="activeColor" />

        <button @click="$emit('restart')" class="back-menu-btn">
            {{ gameOver ? 'Play Again' : 'Back to Menu' }}
        </button>

    </div>
</template>