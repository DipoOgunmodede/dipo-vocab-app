
<template>
    <div>
        Correct words: {{ score }}
    </div>
</template>

<script setup>
import { ref } from 'vue';

let score = ref(0);

const incrementScore = () => {
    score.value++;
};

export { score, incrementScore };
</script>