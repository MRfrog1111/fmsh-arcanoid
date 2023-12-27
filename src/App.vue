<template>
   <h1>{{status}} {{ score }}</h1>
   <GameBoard @score="onScore" />
</template>

<script setup>
import GameBoard from './components/GameBoard.vue'
import { ref } from 'vue'

const score = ref(0)
let status = "Arcanoid"
function onScore()
{
   score.value++
}
</script>