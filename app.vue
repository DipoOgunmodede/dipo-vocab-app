<template>
  {{ gameState }}
  <UContainer>
    <UCard>
      <h1 class="text-2xl">Enter as many {{ currentWordType }} as you can in {{ numberOfSeconds }} seconds (except nothing happens when you run out of time)</h1>
      <UInput class="my-4" v-model="userInputWord" @keyup.enter="checkCurrentWord" @keyup.space="checkCurrentWord" />
      <UButton @click="checkCurrentWord" icon="i-heroicons-book-open" target="_blank">Check word
      </UButton>
      <Timer :seconds="numberOfSeconds" @gameStateChanged="updateGameState" />
    </UCard>
    <!-- message if you enter a word you already tried -->
    <p v-if="validationMessage">{{ validationMessage }}</p>
    <p>Your current score: {{ currentScore }}</p>
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
let previousWords = ref([]);
let validationMessage = ref('');

// watch(userInputWord, () => {
//   lastInputWordValid.value = false;
// });



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
  validationMessage.value = ''; // Reset the validation message

  //this checks the currently input with with the corresponding method 
  const selectWordCheckFunction = wordTypeFunctions[currentWordType.value];
  if (!selectWordCheckFunction) {
    validationMessage.value = 'Invalid word type.';
    return;
  }
  const isWordDuplicated = previousWords.value.includes(userInputWord.value);
  if (isWordDuplicated) {
    validationMessage.value = 'You already entered this word.';
    return;
  }
  // lastInputWordValid.value = selectWordCheckFunction && selectWordCheckFunction().length > 0;
  //check last word entered isn't in previous words
  lastInputWordValid.value = selectWordCheckFunction && selectWordCheckFunction().length > 0;

  //if the word is valid increment score, select a new word type randomly, and set lastwordvalid to true and also checkWordCompleted to true
  console.log(lastInputWordValid.value)
  if (lastInputWordValid.value) {
    incrementScore();
    selectNewWordType();
    //add to previous words
    previousWords.value.push(userInputWord.value);
    lastInputWordValid.value = true;
    userInputWord.value = '';
  }
}

const selectNewWordType = () => {
  const wordTypes = ['adjectives'];
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