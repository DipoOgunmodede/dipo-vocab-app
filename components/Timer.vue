<template>
  <div>
    Time remaining: {{ timeRemaining }}
    <button @click="toggleTimer">{{ timerRunning ? 'Stop' : 'Start' }}</button>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from 'vue';

const props = defineProps({
  seconds: Number,
  triggerTimer: Boolean
});

let timeRemaining = ref(props.seconds);
let countdownIntervalId = null;
let timerRunning = ref(false); // New ref to track if the timer is running
const emit = defineEmits(['gameStateChanged']);

const toggleTimer = () => { // New function to toggle the timer
  if (timerRunning.value) {
    stopTimer();
  } else {
    startTimer();
  }
};

const startTimer = () => {
  timerRunning.value = true;
  countdownIntervalId = setInterval(() => {
    if (timeRemaining.value > 0) {
      timeRemaining.value--;
    } else {
      stopTimer();
      emit('gameStateChanged', 'inactive');
    }
  }, 1000);
  emit('gameStateChanged', 'active');
};

const stopTimer = () => { // New function to stop the timer
  timerRunning.value = false;
  if (countdownIntervalId) {
    clearInterval(countdownIntervalId);
  }
};

// Watch for changes to the triggerTimer prop
watch(() => props.triggerTimer, (newValue) => {
  if (newValue) {
    startTimer();
  } else {
    stopTimer();
  }
});

onUnmounted(() => {
  if (countdownIntervalId) {
    clearInterval(countdownIntervalId);
  }
});
</script>