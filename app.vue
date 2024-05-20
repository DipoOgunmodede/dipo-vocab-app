<template>
  {{gameState}}
  <UContainer>
    <UCard class="mt-10">
      <h1>Enter as many {{ currentWordType }} as you can in {{ numberOfSeconds }} seconds</h1>
      <UInput v-model="userInputWord" @keyup.enter="checkWord" @keyup.space="checkWord" />
      <UButton @click="checkWord" icon="i-heroicons-book-open" target="_blank">Check word
      </UButton>
      <p v-if="checkWordCompleted && userInputWord">{{ validationMessage }}</p>
      <Timer :seconds="numberOfSeconds" @gameStateChanged="updateGameState" />
    </UCard>
    <!-- <ScoreCounter @increment="incrementScore" /> -->
  </UContainer>
</template>

<script setup>
import nlp from 'compromise';
let gameState = ref('inactive');
let userInputWord = ref('');
let numberOfSeconds = 100;
let wordTypes = ['verbs' , 'nouns', 'adjectives', 'adverbs'];
let currentWordType = computed(() => wordTypes[Math.floor(Math.random() * wordTypes.length)]);
let isWordValid = ref(false);
let checkWordCompleted = ref(false);

watch(userInputWord, () => {
  checkWordCompleted.value = false;
});

let validationMessage = computed(() => {
  return isWordValid.value ? 'The word is valid.' : 'The word is not valid.';
});

function updateGameState(newState) {
  gameState.value = newState;
}


function checkWord() {
  const wordTypeFunctions = {
    verbs: () => nlp(userInputWord.value).verbs(),
    nouns: () => nlp(userInputWord.value).nouns(),
    adjectives: () => nlp(userInputWord.value).nouns().adjectives(),
    adverbs: () => nlp(userInputWord.value).verbs().adverbs(),
  };

  const checkFunction = wordTypeFunctions[currentWordType.value];
  isWordValid.value = checkFunction && checkFunction().length > 0;

  // If the word is valid, randomize the word type for the next word
  if (isWordValid.value) {
    currentWordType.value = wordTypes[Math.floor(Math.random() * wordTypes.length)];
    userInputWord.value = ''; // Clear the input field
  }

  checkWordCompleted.value = true;
}
</script>

<style scoped>
/* Add your styles here */
</style>