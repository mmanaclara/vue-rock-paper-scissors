<script setup>
import { ref, computed, onMounted } from "vue";

const wins = ref(0);
const draws = ref(0);
const losses = ref(0);

const choice = ref(null);
const computerChoice = ref(null);
const verdict = ref(null); //Texto exibido ao final

const outcomes = {
  // Resultados, possibilidades
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

const winPercentage = computed(() => {
  const total = wins.value + draws.value + losses.value;
  return total ? (wins.value / total) * 100 : 0;
});

function play(userChoice) {
  choice.value = userChoice;

  const choices = ["rock", "paper", "scissors"];
  const random = Math.floor(Math.random() * choices.length);
  computerChoice.value = choices[random];

  const outcome = outcomes[userChoice][computerChoice.value];

  if (outcome === "win") {
    wins.value++;
    verdict.value = "You win! Yay!";
  } else if (outcome === "loss") {
    losses.value++;
    verdict.value = "You lose! Oh oh! :(";
  } else {
    draws.value++;
    verdict.value = "It is a draw!";
  }

  saveGame();
}

function saveGame() {
  localStorage.setItem("wins", wins.value);
  localStorage.setItem("draws", draws.value);
  localStorage.setItem("losses", losses.value);
}

function loadGame() {
  wins.value = parseInt(localStorage.getItem("wins")) || 0;
  draws.value = parseInt(localStorage.getItem("draws")) || 0;
  losses.value = parseInt(localStorage.getItem("losses")) || 0;
}

function resetRound() {
  choice.value = null;
  computerChoice.value = null;
  verdict.value = null;
}

onMounted(() => {
  loadGame();
  window.addEventListener("keypress", (e) => {
    if (e.key === "r") {
      resetRound();
    }
  });
});
</script>

<template>
  <div class="bg-[#182545] text-gray-50 text-center min-h-screen flex flex-col">
    <header class="container mx-auto pt-14 pb-2 px-6">
      <h1 class="text-4xl font-bold uppercase pt-6">Rock Paper Scissors</h1>
      <span class="text-2xl text-gray-400 mt-4 block">Pick you weapon!</span>
    </header>

    <main class="container mx-auto px-6 flex-1">
      <div
        v-if="choice === null"
        class="flex items-center justify-center -mx-6"
      >
        <button @click="play('rock')" class="mx-4">
          <img
            src="./assets/rockIcon.svg"
            alt="Rock"
            class="w-72 rounded-full"
          />
        </button>

        <button @click="play('paper')" class="mx-4">
          <img
            src="./assets/paperIcon.svg"
            class="w-72 rounded-full"
            alt="Paper"
          />
        </button>

        <button @click="play('scissors')" class="mx-4">
          <img
            src="./assets/scissorsIcon.svg"
            class="w-72 rounded-full"
            alt="Scissors"
          />
        </button>
      </div>

      <div v-else>
        <div class="text-3xl mb-4">
          You picked <span class="font-bold">{{ choice }}</span>
        </div>
        <div class="text-3xl mb-4">
          The computer picked
          <span class="font-bold">{{ computerChoice }}</span>
        </div>
        <div class="text-6xl mb-12">
          {{ verdict }}
        </div>

        <button
          @click="resetRound"
          class="bg-pink-500 text-lg py-2 px-4 rounded-lg"
        >
          Reset
        </button>
      </div>

      <div class="mt-6 text-3xl mb-4">
        {{ wins }} : {{ draws }} : {{ losses }}
      </div>

      <div class="text-lg">Win rate: {{ Math.round(winPercentage) }}%</div>
    </main>
  </div>
</template>
