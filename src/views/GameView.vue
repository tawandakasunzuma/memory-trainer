<script setup>
import { ref } from 'vue'
import GameBoard from '../components/GameBoard.vue'
import ScoreBoard from '../components/ScoreBoard.vue'
import ProgressBar from '../components/ProgressBar.vue'

// Receive difficulty from parent
const props = defineProps({
    difficulty: String
})

// Event emitter to restart game
const emit = defineEmits(['restart'])

// Game state
const sequence = ref([]) // generated color sequence
const userSequence = ref([]) // what user clicks
const level = ref(0) // current level
const score = ref(0) // current score
const isPlaying = ref(false) // true while sequence is shown
const gameOver = ref(false) // true if player lost
const activeColor = ref(null) // currently flashing color

// Lives
const tries = ref(3) // current remaining tries
const maxTries = 3 // maximum allowed tries

// Available colors for game
const colors = ['red', 'blue', 'green', 'yellow']

// Flash speeds by difficulty
const speeds = {
    easy: 600,
    medium: 300,
    hard: 150
}

// High score per difficulty
function getHighScoreKey() {
    return `highScore_${props.difficulty}`
}

// High score
const highScore = ref(Number(localStorage.getItem(getHighScoreKey())) || 0)

// Start game
function startGame() {
    sequence.value = []
    userSequence.value = []
    level.value = 0
    score.value = 0
    gameOver.value = false
    tries.value = maxTries
    nextLevel()  // start first level
}

// Small delay before first level starts
setTimeout(() => {
    nextLevel()
}, 150)

// Go to next level
function nextLevel() {
    level.value++
    userSequence.value = []

    // pick a random color
    const randomColor = colors[Math.floor(Math.random() * colors.length)]
    sequence.value.push(randomColor)

    playSequence() // show sequence to player
}

// Show sequence to user
function playSequence() {
    isPlaying.value = true

    const speed = speeds[props.difficulty]
    const gap = speed * 0.6 // pause between flashes

    sequence.value.forEach((color, index) => {
        setTimeout(() => {
            flashColor(color)
        }, index * (speed + gap))
    })

    const totalTime = sequence.value.length * (speed + gap)

    // allow user input after sequence ends
    setTimeout(() => {
        isPlaying.value = false
    }, totalTime + 600)
}

// Flash button effect
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
    if (isPlaying.value || gameOver.value) return;

    userSequence.value.push(color);
    const index = userSequence.value.length - 1;

    // wrong input
    if (userSequence.value[index] !== sequence.value[index]) {
        tries.value--

        if (tries.value <= 0) {
            gameOver.value = true
            return
        }

        // retry same sequence
        userSequence.value = []
        setTimeout(() => {
            playSequence()
        }, 1200)
        return;

    }

    // correct sequence completed
    if (userSequence.value.length === sequence.value.length) {
        score.value = level.value

        // update high score
        const key = getHighScoreKey()
        if (score.value > highScore.value) {
            highScore.value = score.value
            localStorage.setItem(key, score.value)
        }

        setTimeout(nextLevel, 600)
    }
}
</script>

<template>
    <div class="game-view">

        <!-- Score info -->
        <ScoreBoard :level="level" :score="score" :highScore="highScore" />

        <!-- Progress bar -->
        <ProgressBar :progress="level * 10" />

        <!-- Lives display -->
        <div class="lives">
            <span v-for="n in tries" :key="n" class="heart">
                <img src="../assets/heart.png" alt="Heart icon">
            </span>
        </div>

        <!-- Game buttons -->
        <GameBoard :colors="colors" :isPlaying="isPlaying" :gameOver="gameOver" :handleUserClick="handleUserClick"
            :activeColor="activeColor" />

        <!-- Restart / back button -->
        <button @click="$emit('restart')" class="back-menu-btn">
            {{ gameOver ? 'Play Again' : 'Back to Menu' }}
        </button>

    </div>
</template>