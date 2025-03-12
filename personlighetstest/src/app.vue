<template>
    <div class="container mt-5">
      <h1 class="text-center">Personlighetstest</h1>
      <div v-if="!result">
        <div v-for="(question, index) in questions" :key="index" class="mb-3">
          <p><strong>{{ question.text }}</strong></p>
          <div v-for="option in question.options" :key="option" class="form-check">
            <input class="form-check-input" type="radio" :name="`question-${index}`" :value="option" v-model="userAnswers[index]">
            <label class="form-check-label">{{ option }}</label>
          </div>
        </div>
        <button class="btn btn-primary" @click="calculateResult">Se resultat</button>
      </div>
      <div v-else>
        <h2 class="text-center mt-4">Din personlighetstype er: <span class="text-success">{{ result }}</span></h2>
        <button class="btn btn-secondary mt-3" @click="resetQuiz">Ta testen på nytt</button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue';
  import 'bootstrap/dist/css/bootstrap.min.css';
  
  const questions = [
    { text: 'Hvordan jobber du i et team?', options: ['Jeg bringer nye ideer', 'Jeg samarbeider godt', 'Jeg delegerer oppgaver', 'Jeg er kreativ og løser problemer'] },
    { text: 'Hvordan tar du beslutninger?', options: ['Jeg tar raske beslutninger', 'Jeg analyserer nøye før jeg velger', 'Jeg stoler på ekspertise', 'Jeg følger en plan'] },
    { text: 'Hvordan håndterer du press?', options: ['Jeg holder meg rolig', 'Jeg presser meg selv til perfeksjon', 'Jeg tilpasser meg raskt', 'Jeg fokuserer på målet'] },
  ];
  
  const userAnswers = ref(Array(questions.length).fill(null));
  const result = ref(null);
  
  const personalities = {
    'Jeg bringer nye ideer': 'Resource Investigator',
    'Jeg samarbeider godt': 'Teamworker',
    'Jeg delegerer oppgaver': 'Co-ordinator',
    'Jeg er kreativ og løser problemer': 'Plant',
    'Jeg tar raske beslutninger': 'Shaper',
    'Jeg analyserer nøye før jeg velger': 'Monitor Evaluator',
    'Jeg stoler på ekspertise': 'Specialist',
    'Jeg følger en plan': 'Implementer',
    'Jeg holder meg rolig': 'Monitor Evaluator',
    'Jeg presser meg selv til perfeksjon': 'Completer Finisher',
    'Jeg tilpasser meg raskt': 'Resource Investigator',
    'Jeg fokuserer på målet': 'Shaper',
  };
  
  const calculateResult = () => {
    const counts = {};
    userAnswers.value.forEach(answer => {
      if (answer) {
        counts[answer] = (counts[answer] || 0) + 1;
      }
    });
    const topAnswer = Object.keys(counts).reduce((a, b) => (counts[a] > counts[b] ? a : b), null);
    result.value = personalities[topAnswer] || 'Ukjent';
  };
  
  const resetQuiz = () => {
    result.value = null;
    userAnswers.value.fill(null);
  };
  </script>
  