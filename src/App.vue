<script setup>
import { ref, computed, onMounted } from "vue";

const wins = ref(0);
const draws = ref(0);
const losses = ref(0);

const choice = ref(null);
const computerChoice = ref(null);
const verdict = ref(null);

const outcomes = {
  rock: {
    rock: "draw",
    paper: "loss",
    scissors: "win",
  },
  paper: {
    rock: "win",
    paper: "draw",
    scissors: "loss",
  },
  scissors: {
    rock: "loss",
    paper: "win",
    scissors: "draw",
  },
};

const play = (c) => {
  choice.value = c;

  const choices = ["rock", "paper", "scissors"];
  const random = Math.floor(Math.random() * choices.length);
  computerChoice.value = choices[random];

  const outcome = outcomes[choice.value][computerChoice.value];

  if (outcome === "win") {
    wins.value++;
    verdict.value = "You win!";
  } else if (outcome === "loss") {
    losses.value++;
    verdict.value = "You Lose!";
  } else {
    draws.value++;
    verdict.value = "It's a draw";
  }

  SaveGame();
};

const winPercentage = computed(() => {
  const total = wins.value + draws.value + losses.value;
  return total ? (wins.value / total) * 100 : 0;
});

const SaveGame = () => {
  localStorage.setItem("wins", wins.value);
  localStorage.setItem("draws", draws.value);
  localStorage.setItem("losses", losses.value);
};

const LoadGame = () => {
  wins.value = parseInt(localStorage.getItem("wins")) || 0;
  draws.value = parseInt(localStorage.getItem("draws")) || 0;
  losses.value = parseInt(localStorage.getItem("losses")) || 0;
};

const ResetRound = () => {
  choice.value = null;
  computerChoice.value = null;
  verdict.value = null;
};

const ResetStorage = () => {
  choice.value = null;
  computerChoice.value = null;
  verdict.value = null;
  localStorage.removeItem("wins");
  localStorage.removeItem("draws");
  localStorage.removeItem("losses");
  wins.value = 0;
  draws.value = 0;
  losses.value = 0;
};

onMounted(() => {
  LoadGame();

  window.addEventListener("keypress", (e) => {
    if (e.key === "r") {
      ResetRound();
    }
  });
});
</script>

<template>
  <div class="bg-gray-900 text-white text-center min-h-screen flex flex-col">
    <header class="flex container mx-auto p-6 items-center justify-center mt-15 md:mt-20">
      <img
        src="./assets/all.svg"
        alt="Rock"
        class="w-[200px] h-[100px] md:w-[200px] md:h-[100px] flex items-center justify-center"
      />

      <h1 class="text-3xl md:text-4xl font-bold">Rock, Paper, Scissors!</h1>
    </header>

    <main class="container mx-auto p-6 flex-1">
      <div v-if="choice === null" class="flex items-center justify-center">
        <button
          @click="play('rock')"
          class="bg-white rounded-full shadow-lg w-48 p-12 mx-2 md:mx-6 transition-colors duration-300 hover:bg-blue-500"
        >
          <img src="./assets/rock.svg" alt="Rock" class="w-full" />
        </button>

        <button
          @click="play('paper')"
          class="bg-white rounded-full shadow-lg w-48 p-12 mx-2 md:mx-6 transition-colors duration-300 hover:bg-red-500"
        >
          <img src="./assets/paper.svg" alt="Paper" />
        </button>

        <button
          @click="play('scissors')"
          class="bg-white rounded-full shadow-lg w-48 p-12 mx-2 md:mx-6 transition-colors duration-300 hover:bg-green-500"
        >
          <img src="./assets/scissors.svg" alt="Scissors" />
        </button>
      </div>

      <div v-else>
        <div class="text-3xl mb-4">
          You picked <span class="text-gray-400">{{ choice }}</span>
        </div>
        <div class="text-3xl mb-4">
          The computer picked
          <span class="text-gray-400">{{ computerChoice }}</span>
        </div>
        <div class="text-6xl mb-12">
          {{ verdict }}
        </div>

        <button @click="ResetRound" class="bg-gray-700 text-lg py-2 px-4">
          New Game
        </button>
      </div>

      <div class="mt-8 text-3xl mb-4">
        <span class="text-[20px] text-gray-400">wins:</span> {{ wins }}
        <span class="text-[20px] text-gray-400"> | draws:</span> {{ draws }}
        <span class="text-[20px] text-gray-400"> | losses:</span>
        {{ losses }}
      </div>

      <div class="text-lg flex flex-row justify-center items-center">
        Win rate: {{ Math.round(winPercentage) }}%
        <button @click="ResetStorage" class="bg-gray-700 text-xs py-2 px-4 ml-3">
          Reset Values
        </button>
      </div>
    </main>
  </div>
</template>

<style></style>
