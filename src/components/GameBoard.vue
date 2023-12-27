<template>
   <div class="game-board">
      <div class="board-row" v-for="(row, rowIdx) in Board" :key="rowIdx">
         <div class="board-cell" :class="{
            'platform': cell == 1,
            'ball': cell == 2,
            'target': cell == 3
         }" v-for="(cell, cellIdx) in row" :key="cellIdx">
            &nbsp;
         </div>
      </div>
   </div>
</template>

<script setup>
import { onBeforeMount, onMounted, reactive, ref } from 'vue'

const emit = defineEmits(['score'])

const Board = reactive([])

const boardWidth = ref(25)
const boardHeight = ref(20)
const posP= ref(10)
const lenP = ref(4)
const moveP = ref(0) 
const ballY = ref(6)
const ballX = ref(13)
const ballDirectionY = ref(-1)
const ballDirectionX = ref(1)
//1 0 -1
var interval = 0

onBeforeMount(() =>
{
   InitBoard()
   for(let i = 0; i < 3; i++){
      Board[4][5+i] = 3
   }
   window.addEventListener('keydown', onKeyDown)
})

onMounted(() =>
{
   interval = setInterval(() => RenderGame(), 250)
})

function onKeyDown(evt)
{
   switch (evt.key)
   {
      case 'ArrowLeft':
         if (moveP.value != -1)
         {
           moveP.value = -1
         }
         break
      case 'ArrowRight':
      if (moveP.value != 1)
         {
           moveP.value = 1
         }
         break
      case 'ArrowDown':
      if (moveP.value != 0)
         {
           moveP.value = 0
         }
         break
      default:
         break
   }
}

function InitBoard()
{
   for (let y = 0; y < boardHeight.value; y++)
   {
      let row = []
      for (let x = 0; x < boardWidth.value; x++)
      {
         row.push(0)
      }
      Board.push(row)
   }
}

function ChangeBallDirection(bumpX,bumpY){
     if(Math.abs(ballY.value-bumpY) == 1 || ballY.value-bumpY == 0){
      ballDirectionY.value *= -1
     }
     if(Math.abs(ballX.value-bumpX) == 1 || ballX.value-bumpX == 0){
      ballDirectionX.value *= -1
     }
}

function RenderGame()
{
   Board[12].forEach((cell, idx) => Board[12][idx] = 0)
   Board[ballY.value][ballX.value] = 0 //очищаем всю строку
   if(ballY.value+1==12 && ballX.value >= posP.value && ballX.value <= posP.value+lenP.value){
      ChangeBallDirection(ballX,12);
   }
   if(ballY.value-1 == 0){
      ChangeBallDirection(ballX,0);
   }
   if(ballX.value-1 == 0){
      ChangeBallDirection(0,ballY);
   }
   if(ballX.value+1 == boardWidth.value ){
      ChangeBallDirection(boardWidth.value,ballY);
   }
   if(ballY.value+1 == boardHeight.value){
      GameOver();
   }
   if(Board[ballY.value+ballDirectionY.value][ballX.value+ ballDirectionX.value] == 3){
      emit('score')
   }
   posP.value += moveP.value;
   ballX.value += ballDirectionX.value;
   ballY.value -= ballDirectionY.value;
   Board[ballY.value][ballX.value] = 2
   for(let i = posP.value; i < posP.value+lenP.value;i++){
      Board[12][i] = 1
   }
}


function GameOver()
{
   clearInterval(interval)
   console.log("Game Over")
}
</script>

<style>
.board-cell {
   width: 20px;
   height: 20px;
   display: inline-block;
   border: 1px dotted #ccc;
}

.ball {
   background-color: red;
}

.platform {
   background-color: blue;
}

.target {
   background-color: green;
}
</style>