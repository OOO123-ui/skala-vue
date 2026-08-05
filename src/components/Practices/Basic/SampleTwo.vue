<script setup>
import { ref } from 'vue'

const welcomeMessage = ref('Welcome to Skala-Vue')
</script>

<template>
    <div class="practice-section">
        <h2>{{ welcomeMessage.value }}</h2>
        <p>{{ welcomeMessage.value.toUpperCase() }}</p>
        <p>{{ 'Random number: ' + Math.ceil(Math.random()*100) }}</p>
    </div>
</template>