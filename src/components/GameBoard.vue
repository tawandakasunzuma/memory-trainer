<script setup>
defineProps({
    colors: Array, // list of colors
    isPlaying: Boolean, // game running
    gameOver: Boolean, // game ended
    handleUserClick: Function, // click handler
    activeColor: String // current active color
})
</script>

<template>
    <div class="grid">
        <button v-for="color in colors" :key="color" :class="[
            'color',
            color,
            {
                dimmed: isPlaying,
                active: activeColor === color
            }
        ]" @click="handleUserClick(color)" :disabled="gameOver">
        </button>
    </div>
</template>