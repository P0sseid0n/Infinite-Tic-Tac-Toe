<script setup lang="ts">
import { computed, reactive, ref, toRaw } from 'vue'
import XIcon from './components/XIcon.vue'
import OIcon from './components/OIcon.vue'

const turn = ref<'X' | 'O'>('X')
const tiles = reactive<Array<'X' | 'O' | null>>(Array(9).fill(null))
const history = reactive<number[]>([])
const toRemove = computed(() => {
  if (history.length >= 6) return history[0]
  else return null
})

function checkWin() {
  const winningCombinations = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ]

  for (const combination of winningCombinations) {
    const [a, b, c] = combination

    if (tiles[a] === tiles[b] && tiles[b] === tiles[c] && tiles[a] !== null) {
      return tiles[a]
    }
  }

  return null
}

const handleClick = (index: number) => {
  if (tiles[index] !== null) return

  tiles[index] = turn.value
  turn.value = turn.value === 'X' ? 'O' : 'X'

  if (checkWin() !== null) {
    console.warn('WINNER')
  }

  if (toRemove.value !== null) {
    tiles[toRemove.value] = null
    history.shift()
  }

  history.push(index)
}
</script>

<template>
  <header>
    <div class="config"></div>
    <h1>Tic Tac Toe</h1>
    <div class="turn-view">
      <p class="xTurn" :class="{ turn: turn === 'X' }">
        <XIcon />
      </p>
      <div class="divisor"></div>
      <p class="oTurn" :class="{ turn: turn === 'O' }">
        <OIcon />
      </p>
    </div>
    {{ toRemove }}
  </header>
  <main>
    <div class="board">
      <button
        v-for="(tile, index) in tiles"
        :key="index"
        class="tile"
        :class="{
          xTurn: tiles[index] === 'X',
          oTurn: tiles[index] === 'O',
          remove: index === toRemove,
        }"
        @click="() => handleClick(index)"
      >
        <XIcon v-if="tile === 'X'" />
        <OIcon v-else-if="tile === 'O'" />
      </button>

      <div class="divider"></div>
      <div class="divider"></div>
      <div class="divider"></div>
      <div class="divider"></div>
    </div>
  </main>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:ital,wght@0,100..800;1,100..800&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;

  font-family: 'JetBrains Mono', system-ui;
}

:root {
  --x-color: black;
  --o-color: black;
}

#app {
  height: 100vh;
  background-color: #f9fafb;
  display: flex;
  flex-direction: column;
}

.xTurn {
  color: var(--x-color);
}

.oTurn {
  color: var(--o-color);
}

header {
  height: 15vh;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
  align-items: center;
}

header .turn-view {
  display: flex;
  width: 100%;
  max-width: 128px;
}

header .turn-view .divisor {
  width: 2px;
  background-color: #e5e7eb;
}

header .turn-view p {
  width: 50%;
  text-align: center;
  opacity: 0.1;
  padding: 8px 0;
  font-size: 1.5rem;
}

header .turn-view p.turn {
  opacity: 1;
}

main {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 85vh;
}

.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 1fr);

  width: 100%;
  max-width: 480px;
  aspect-ratio: 1 / 1;
  position: relative;
}

button.tile {
  border: none;
  cursor: pointer;
  font-size: clamp(2rem, 10vw, 4rem);
  outline: none;
  background-color: #f9fafb;
}

button.tile.remove {
  opacity: 0.2;
}

.board .divider {
  border-radius: 8px;
  background-color: #e5e7eb;
  position: absolute;
}

.board .divider:nth-of-type(1),
.board .divider:nth-of-type(2) {
  width: 4px;
  height: 100%;
}

.board .divider:nth-of-type(3),
.board .divider:nth-of-type(4) {
  width: 100%;
  height: 4px;
}

.board .divider:nth-of-type(1) {
  left: calc(100% / 3 * 1);
}

.board .divider:nth-of-type(2) {
  left: calc(100% / 3 * 2);
}

.board .divider:nth-of-type(3) {
  top: calc(100% / 3 * 1);
}

.board .divider:nth-of-type(4) {
  top: calc(100% / 3 * 2);
}
</style>
