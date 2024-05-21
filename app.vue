<template>
  {{ gameState }}
  <UContainer>
    <UCard class="mt-10">
      <h1>Enter as many {{ currentWordType }} as you can in {{ numberOfSeconds }} seconds</h1>
      <UInput v-model="userInputWord" @keyup.enter="checkCurrentWord" @keyup.space="checkCurrentWord" />
      <UButton @click="checkCurrentWord" icon="i-heroicons-book-open" target="_blank">Check word
      </UButton>
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
let currentWordType = ref('');
let currentScore = ref(0);
let lastInputWordValid = ref(false);

// watch(userInputWord, () => {
//   lastInputWordValid.value = false;
// });

let validationMessage = computed(() => {
  return lastInputWordValid.value ? 'The word is valid.' : 'The word is not valid.';
});

function updateGameState(newState) {
  gameState.value = newState;
}

const wordTypeFunctions = {
  verbs: () => nlp(userInputWord.value).verbs(),
  nouns: () => nlp(userInputWord.value).nouns(),
  adjectives: () => nlp(userInputWord.value).adjectives(),
  adverbs: () => nlp(userInputWord.value).adverbs(),
};

function checkCurrentWord() {
  //this checks the currently input with with the corresponding method 
  const selectWordCheckFunction = wordTypeFunctions[currentWordType.value];
  console.log('checking word');
  console.log(selectWordCheckFunction())
  lastInputWordValid.value = selectWordCheckFunction && selectWordCheckFunction().length > 0;
  //if the word is valid increment score, select a new word type randomly, and set lastwordvalid to true and also checkWordCompleted to true
  console.log(lastInputWordValid.value)
  if (lastInputWordValid.value) {
    incrementScore();
    selectNewWordType();
    lastInputWordValid.value = true;
    userInputWord.value = '';
  }
}

const selectNewWordType = () => {
  const wordTypes = ['adjectives' ];
  currentWordType.value = wordTypes[Math.floor(Math.random() * wordTypes.length)];
};




function incrementScore() {
  console.log('incrementing score');
  currentScore.value += 1;
}

onMounted(selectNewWordType);

</script>

<style scoped>
/* Add your styles here */
</style>