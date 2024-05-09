<template>
  <UContainer>
    <UCard class="mt-10">
      <h1>Enter as many {{ randomisedWordType }} as you can in {{ numberOfSeconds }} seconds</h1>
      <UInput v-model="userInputWord" @keyup.enter="checkWord" @keyup.space="checkWord" />
      <UButton @click="checkWord" icon="i-heroicons-book-open" target="_blank">Check word
      </UButton>
      <p v-if="checkWordCompleted && userInputWord">{{ validationMessage }}</p>
    </UCard>
    <!-- <ScoreCounter @increment="incrementScore" /> -->
  </UContainer>
</template>

<script setup>
import { ref, computed, watch, defineEmits } from 'vue';
import nlp from 'compromise';

let userInputWord = ref('');
let numberOfSeconds = 120;
let wordTypes = ['verbs'];
let randomisedWordType = wordTypes[Math.floor(Math.random() * wordTypes.length)];
let isWordValid = ref(false);
let checkWordCompleted = ref(false);

watch(userInputWord, () => {
  checkWordCompleted.value = false;
});

let validationMessage = computed(() => {
  return isWordValid.value ? 'The word is valid.' : 'The word is not valid.';
});

function checkWord() {
  const wordTypeFunctions = {
    verbs: () => nlp(userInputWord.value).verbs(),
    nouns: () => nlp(userInputWord.value).nouns(),
    adjectives: () => nlp(userInputWord.value).nouns().adjectives(),
    adverbs: () => nlp(userInputWord.value).verbs().adverbs(),
  };

  const checkFunction = wordTypeFunctions[randomisedWordType];
  isWordValid.value = checkFunction && checkFunction().length > 0;
  checkWordCompleted.value = true;
}
</script>

<style scoped>
/* Add your styles here */
</style>