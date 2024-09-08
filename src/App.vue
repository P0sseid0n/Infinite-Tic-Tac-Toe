<script setup lang="ts">
import { computed, reactive, ref, toRaw } from 'vue'
import XIcon from './components/XIcon.vue'
import OIcon from './components/OIcon.vue'

import GearIcon from './components/GearIcon.vue'

const turn = ref<'X' | 'O'>('X')
const tiles = reactive<Array<'X' | 'O' | null>>(Array(9).fill(null))
const history = reactive<number[]>([])
const toRemove = computed(() => {
  if (history.length >= 6) return history[0]
  else return null
})
const configShown = ref(false)
const winner = computed(() => {
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
})

const handleClick = (index: number) => {
  if (tiles[index] !== null || winner.value !== null) return

  tiles[index] = turn.value
  turn.value = turn.value === 'X' ? 'O' : 'X'

  if (winner.value !== null) {
    return console.warn('WINNER')
  }

  if (toRemove.value !== null) {
    tiles[toRemove.value] = null
    history.shift()
  }

  history.push(index)
}

const handleRestart = () => {
  tiles.fill(null)
  history.splice(0, history.length)
  turn.value = 'X'
}
</script>

<template>
  <header>
    <div>
      <button class="config" @click="configShown = true">
        <GearIcon />
      </button>

      <div class="overlay-modal" :class="{ shown: configShown }" @click.self="configShown = false">
        <div class="config-modal">
          <div class="theme">
            <button class="active">Claro</button>
            <button>Escuro</button>
          </div>
        </div>
      </div>
    </div>
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
  </header>
  <main>
    <div class="overlay-modal" :class="{ shown: winner !== null }">
      <div class="win-modal">
        <h2>
          <div>
            <XIcon v-if="winner === 'X'" />
            <OIcon v-else-if="winner === 'O'" />
          </div>
          <span>venceu!</span>
        </h2>
        <button @click="handleRestart">Recomeçar Jogo</button>
      </div>
    </div>

    <div class="board">
      <button
        v-for="(tile, index) in tiles"
        :key="index"
        class="tile"
        :class="{
          xTurn: tiles[index] === 'X',
          oTurn: tiles[index] === 'O',
          remove: index === toRemove && tiles[index] !== winner,
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
  <footer>
    <p>Open source no <a href="https://github.com/P0sseid0n/InfiniteTicTacToe" target="_blank" rel="noopener noreferrer">Github</a></p>
  </footer>
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
  height: 25vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  padding-top: 16px;
}

header .config {
  display: flex;
  gap: 32px;
}

header .config {
  background-color: #f9fafb;
  border: 1px solid #e5e7eb;
  cursor: pointer;
  font-size: 1.25rem;
  width: 32px;
  height: 32px;
  border-radius: 4px;
  display: flex;
  justify-content: center;
  align-items: center;
  box-shadow: rgba(0, 0, 0, 0.05) 0px 1px 2px 0px;
  transition: background-color 0.2s;
  z-index: 1000;
}

header .config:hover {
  border-color: black;
}

.overlay-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: none;
  justify-content: center;
  align-items: center;
  z-index: 999;
  background-color: rgba(0, 0, 0, 0.05);
}

.overlay-modal.shown {
  display: flex;
}

.overlay-modal > div {
  display: flex;
  flex-direction: column;
  position: absolute;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #f9fafb;
  border: 1px solid #e5e7eb;
  border-radius: 4px;
  box-shadow: rgba(0, 0, 0, 0.05) 0px 1px 2px 0px;
  width: 100%;
  max-width: 320px;
  padding: 16px;
}

.config-modal {
  top: 96px;
}

.config-modal .theme {
  width: 100%;
  background-color: #e5e7eb;
  padding: 8px;
  border-radius: 4px;
}

.config-modal .theme button {
  background-color: #e5e7eb;
  width: 50%;
  border: none;
  border-radius: 4px;
  padding: 8px 16px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.config-modal .theme button.active {
  background-color: #f9fafb;
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
  transition: opacity 0.5s;
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

.win-modal {
  top: 50%;
}

.win-modal h2 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  gap: 8px;
}

.win-modal div {
  font-size: 2rem;
}

.win-modal button {
  background-color: #f9fafb;
  border: 1px solid #e5e7eb;
  border-radius: 4px;
  padding: 8px 16px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.win-modal button:hover {
  border-color: black;
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

footer {
  height: 10vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

footer a {
  color: #000;
  text-decoration: none;
  font-weight: bolder;
}

footer a:hover {
  text-decoration: underline;
}
</style>
