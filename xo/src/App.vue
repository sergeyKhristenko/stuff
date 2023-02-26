<script setup>
import { ref, reactive, computed, watch } from 'vue';
const initialBoard = [null, null, null, null, null, null, null, null, null];
// const initialBoard = ['X', 'X', 'X', '0', '0', 'X', 'X', '0', null];
const winTypes = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 4, 8],
  [2, 4, 6],
];

const players = reactive({
  X: { name: 'X', sign: 'X', wins: 0 },
  O: { name: 'O', sign: 'O', wins: 0 },
});
const player = ref(players.X.sign);

const board = ref([...initialBoard]);

const isBoardFilled = computed(() => {
  return !!(winner.value || board.value.every((o) => !!o));
});

const winner = computed(() => {
  let _player = '';
  const coordinates = winTypes.find((indexes) => {
    _player = board.value[indexes[0]];
    if (!_player) return;

    return (
      board.value[indexes[0]] === _player &&
      board.value[indexes[1]] === _player &&
      board.value[indexes[2]] === _player
    );
  });
  if (!coordinates) return;

  players[_player].wins++;

  return { player: players[_player], coordinates };
});

function getClass(i) {
  const midRows = [4, 5, 6];
  const midCols = [2, 5, 8];

  const index = i + 1;
  let classList = '';

  if (midCols.includes(index)) {
    classList += 'right left ';
  }
  if (midRows.includes(index)) {
    classList += 'top bottom ';
  }

  return classList;
}

function setValue(index) {
  if (board.value[index] || isBoardFilled.value) {
    return;
  }
  board.value[index] = player.value;
  player.value =
    player.value === players.X.sign ? players.O.sign : players.X.sign;
}

function resetBoard() {
  board.value = [...initialBoard];
  player.value = players.X.sign;
}

function setName(event, player) {
  player.name = event.target.textContent;
}
</script>

<template>
  <div class="container">
    <div class="board-container">
      <div class="players-container">
        <div v-for="player in players" :key="player.sign" class="player-name">
          <span
            class="input"
            contenteditable
            @input="(event) => setName(event, player)"
            >{{ player.name }}</span
          >
          wins: {{ player.wins }}
        </div>
      </div>
      <div class="board">
        <div
          v-for="(cell, i) of board"
          :key="i"
          class="cell"
          :class="[
            getClass(i),
            winner?.coordinates?.includes(i) ? 'win-color' : null,
          ]"
          @click="setValue(i)"
        >
          <span class="cell" :class="!isBoardFilled ? 'border-round' : null">{{
            cell
          }}</span>
        </div>
      </div>
    </div>
    <div v-if="isBoardFilled" class="alert font-big">
      <p v-if="winner" style="margin-bottom: 0px;">
        <span class="win-color">{{ winner.player.name }}</span> wins!
      </p>
      <p v-else>DRAW</p>
      <button @click="resetBoard()" class="playagain-button">play again</button>
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Finger+Paint&display=swap');

body {
  margin: 0;
}
</style>
<style scoped lang="scss">
.container {
  padding: 8px;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  font-family: 'Finger Paint', cursive;

  background: ghostwhite;
  color: dimgray;
  text-align: center;
}
.board {
  display: flex;
  flex-wrap: wrap;
  margin: auto;
  border-radius: 15px;
  padding: 100px;
  width: 304px;
  background-color: ghostwhite;
  color: black;
  position: relative;
  box-shadow: rgba(255, 255, 255, 0.1) 0px 1px 1px 0px inset,
    rgba(50, 50, 93, 0.25) 0px 50px 100px -20px,
    rgba(0, 0, 0, 0.3) 0px 30px 60px -30px;
}
.cell {
  width: 100px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 75px;
  cursor: not-allowed;
}
.border-round {
  cursor: pointer;
  border-radius: 50%;
  &:hover {
    background-color: gainsboro;
  }
}
.bottom {
  border-bottom: 2px solid gray;
}
.right {
  border-right: 2px solid gray;
}
.left {
  border-left: 2px solid gray;
}
.top {
  border-top: 2px solid gray;
}
.win-color {
  color: red;
}
.font-big {
  font-size: 25px;
}

// modal
.alert {
  height: 75px;
  display: inline-block;
  // background: ghostwhite;
  position: absolute;
  z-index: 10;
  border-radius: 20px;
  animation: larger 0.5s;
  animation-fill-mode: forwards;
  box-sizing: content-box;
  padding: 30px;
  top: 5%;
}

.player-name {
  margin: 4px;
}
.input {
  border: 1px solid #ccc;
  background-color: white;
  min-height: 30px;
  font-family: inherit;
  font-size: inherit;
  padding: 1px 6px;
}
.players-container {
  display: flex;
  justify-content: space-between;
  margin: 20px;
}
.board-container {
  width: 500px;
}
.playagain-button {
  font-family: inherit;
  cursor: pointer;
}
</style>
