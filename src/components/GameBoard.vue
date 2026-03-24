<script setup>
defineProps({
    colors: Array,
    isPlaying: Boolean,
    gameOver: Boolean,
    handleUserClick: Function,
    activeColor: String
})
</script>

<template>
    <div class="grid">
        <button
        v-for="color in colors"
        :key="color"
        :class="[
            'color',
            color,
            {
            dimmed: isPlaying,
            active: activeColor === color
            }
        ]"
        @click="handleUserClick(color)"
        :disabled="gameOver"
        ></button>
    </div>
</template>