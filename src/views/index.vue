<template>
  <div class="px-8">
    <div class="absolute top-10 left-10">
      <span class="text-3xl text-gray-300 mr-12"
        >O: <span class="text-red-400">{{ winningTimes.player1 }}</span></span
      >
      <span class="text-3xl text-gray-300"
        >X: <span class="text-red-400">{{ winningTimes.player2 }}</span></span
      >
    </div>
    <div class="flex justify-center flex-col">
      <div class="mb-4 flex justify-between items-center">
        <span class="text-3xl md:text-5xl text-gray-300"
          >It's the
          <span class="text-red-400">{{
            whichOnesTurn === "player1" ? "o" : "x"
          }}</span>
          turn</span
        >
        <button
          @click="resetGame"
          class="text-2xl md:text-3xl bg-slate-300 text-zinc-900 px-6 py-2 rounded-xl shadow-xl"
        >
          reset
        </button>
      </div>
      <div
        class="flex gap-5 flex-wrap w-[22rem] h-[22rem] md:w-[30rem] md:h-[30rem] overflow-hidden"
      >
        <button
          @click="slelectAction(action)"
          :disabled="player2.has(action) || player1.has(action) || winner.player !== ''"
          :class="{
            'cursor-not-allowed': player2.has(action) || player1.has(action),
            '!bg-zinc-800': winner.actions.includes(action),
          }"
          class="bg-slate-300 shadow-xl rounded-xl w-[calc(33.333%-.84rem)] h-[calc(33.333%-.84rem)] flex justify-center items-center border border-zinc-900 cursor-pointer"
          v-for="action in 9"
          :key="action"
        >
          <span
            v-if="player2.has(action)"
            class="text-zinc-900 text-9xl bg-transparent mb-4"
            :class="{ '!text-slate-300': winner.actions.includes(action) }"
            >&#215;</span
          >
          <span
            v-if="player1.has(action)"
            class="w-14 h-14 rounded-full border-[6px] border-zinc-900"
            :class="{ '!border-slate-300': winner.actions.includes(action) }"
          ></span>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { watch } from "vue";
import { ref } from "vue";
type Winner = { player: string; actions: number[] };
type WinngingTimes = { player1: number; player2: number };

// player1 === O
// player2 === X

const player1 = ref<Set<number>>(new Set([]));
const player2 = ref<Set<number>>(new Set([]));
const whichOnesTurn = ref<string>("player1");
const winningTimes = ref<WinngingTimes>({
  player1: 0,
  player2: 0,
});
const winner = ref<Winner>({
  player: "",
  actions: [],
});

const winningConditions: number[][] = [
  [1, 5, 9],
  [3, 5, 7],
  [1, 2, 3],
  [1, 4, 7],
  [2, 5, 8],
  [3, 6, 9],
  [4, 5, 6],
  [7, 8, 9],
];

function slelectAction(action: number) {
  if (whichOnesTurn.value === "player1") {
    player1.value.add(action);
    whichOnesTurn.value = "player2";
  } else {
    player2.value.add(action);
    whichOnesTurn.value = "player1";
  }
}

function chooseWinner(player: string, actions: number[]) {
  winner.value.player = player;
  winner.value.actions = actions;
}

function resetGame() {
  winner.value.player = "";
  winner.value.actions = [];
  player1.value.clear();
  player2.value.clear();
  whichOnesTurn.value = "player1";
}

watch([player2.value, player1.value], () => {
  winningConditions.forEach((condition) => {
    const compairPlayer1Actions = new Set(
      condition.filter((action) => player1.value.has(action))
    );
    const compairPlayer2Actions = new Set(
      condition.filter((action) => player2.value.has(action))
    );
    const isPlayer1Win = compairPlayer1Actions.size > 2;
    const isPlayer2Win = compairPlayer2Actions.size > 2;

    if (isPlayer1Win) {
      chooseWinner("player1", [...compairPlayer1Actions]);
      winningTimes.value.player1++;
    } else if (isPlayer2Win) {
      chooseWinner("player2", [...compairPlayer2Actions]);
      winningTimes.value.player2++;
    }
  });
});
</script>

<style scoped></style>
