<template>
    <div>
      Time remaining: {{ timeRemaining }}
      <button @click="startTimer">Start</button>
    </div>
  </template>
  
  <script setup>
  import { ref, onUnmounted } from 'vue';
  
  const props = defineProps({
    seconds: Number
  });
  
  let timeRemaining = ref(props.seconds);
  let countdownIntervalId = null;
  const emit = defineEmits(['gameStateChanged`']);
  const startTimer = () => {
    countdownIntervalId = setInterval(() => {
      if (timeRemaining.value > 0) {
        timeRemaining.value--;
      } else {
        clearInterval(countdownIntervalId);
        emit('gameStateChanged', 'inactive');
      }
    }, 1000);
    emit('gameStateChanged', 'active');
  };
  
  onUnmounted(() => {
    if (countdownIntervalId) {
      clearInterval(countdownIntervalId);
    }
  });
  </script>