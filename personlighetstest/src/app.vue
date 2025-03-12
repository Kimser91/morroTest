<template>
  <div class="container d-flex flex-column justify-content-center align-items-center vh-100">
    <h1 class="text-center">Personlighetstest</h1><br/>
    <h2 class="text-center">Gi poeng etter hva som stemmer best med deg!</h2><br/>
    <h4 class="text-center"> 1 Ikke Enig - 5 Helt Enig</h4>
    <div v-if="!result">
      <div v-if="questions.length > 0" class="card p-4 text-center fixed-card">
        <p class="question-text"><strong>{{ currentQuestionIndex +1 }}. {{ questions[currentQuestionIndex].text }}</strong></p>
        <div class="d-flex justify-content-center">
          <span>1</span>
          <input type="range" min="1" max="5" step="1" class="form-range mx-3" v-model="userAnswers[currentQuestionIndex]">
          <span>5</span>
        </div>
        <button class="btn btn-primary mt-3" @click="nextQuestion">
          {{ currentQuestionIndex < questions.length - 1 ? 'Neste' : 'Se resultat' }}
        </button>
      </div>
      <div v-else>
        <p>Laster spørsmål...</p>
      </div>
    </div>
    <div v-else>
      <h2 class="text-center">Din personlighetstype er: <span class="text-success">{{ result }}</span></h2>
      <button class="btn btn-secondary mt-3" @click="resetQuiz">Ta testen på nytt</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import 'bootstrap/dist/css/bootstrap.min.css';

const questions = [
  { text: 'Jeg liker å jobbe i team.' },
  { text: 'Jeg tar raske beslutninger.' },
  { text: 'Jeg håndterer stress godt.' },
  { text: 'Jeg liker å planlegge ting på forhånd.' },
  { text: 'Jeg foretrekker å følge regler fremfor å improvisere.' },
  { text: 'Jeg liker å ta initiativ i prosjekter.' },
  { text: 'Jeg er kreativ og finner nye løsninger.' },
  { text: 'Jeg trives med å jobbe alene.' },
  { text: 'Jeg liker å analysere problemer grundig før jeg handler.' },
  { text: 'Jeg foretrekker å følge en fast struktur i arbeidet mitt.' },
  { text: 'Jeg føler meg komfortabel med å ta beslutninger for en gruppe.' },
  { text: 'Jeg er flink til å lytte til andres meninger.' },
  { text: 'Jeg liker å perfeksjonere arbeidet mitt før jeg leverer det.' },
  { text: 'Jeg trives med å lære nye ferdigheter og spesialisere meg.' },
  { text: 'Jeg er flink til å tilpasse meg endringer.' },
  { text: 'Jeg liker å delegere oppgaver til andre.' },
  { text: 'Jeg foretrekker å jobbe med detaljer fremfor helheten.' },
  { text: 'Jeg er god på å samarbeide og få teamet til å fungere godt.' },
  { text: 'Jeg liker å jobbe raskt og effektivt.' },
  { text: 'Jeg verdsetter nøyaktighet og presisjon i arbeidet mitt.' },
  { text: 'Jeg motiveres av å oppnå gode resultater.' },
  { text: 'Jeg trives best med praktiske oppgaver fremfor teoretiske analyser.' },
  { text: 'Jeg foretrekker å følge en langsiktig plan fremfor å improvisere.' },
  { text: 'Jeg liker å hjelpe andre med deres oppgaver.' },
  { text: 'Jeg trives best i en strukturert arbeidsdag.' },
  { text: 'Jeg liker å eksperimentere med nye måter å gjøre ting på.' },
  { text: 'Jeg liker å jobbe med mennesker og bygge relasjoner.' },
  { text: 'Jeg er flink til å se løsninger der andre ser problemer.' },
  { text: 'Jeg liker å sette klare mål for meg selv og andre.' }
];

const userAnswers = ref(Array(questions.length).fill(3));
const currentQuestionIndex = ref(0);
const result = ref(null);

const nextQuestion = () => {
  if (currentQuestionIndex.value < questions.length - 1) {
    currentQuestionIndex.value++;
  } else {
    calculateResult();
  }
};

const calculateResult = () => {
  const personalityScores = {
    'Resource Investigator': 0,
    'Teamworker': 0,
    'Co-ordinator': 0,
    'Plant': 0,
    'Shaper': 0,
    'Monitor Evaluator': 0,
    'Specialist': 0,
    'Implementer': 0,
    'Completer Finisher': 0
  };

  userAnswers.value.forEach((score, index) => {
    const personalities = Object.keys(personalityScores);
    personalityScores[personalities[index % personalities.length]] += score;
  });

  const bestPersonality = Object.keys(personalityScores).reduce((a, b) => 
    personalityScores[a] > personalityScores[b] ? a : b);

  result.value = bestPersonality;
};

const resetQuiz = () => {
  result.value = null;
  currentQuestionIndex.value = 0;
  userAnswers.value.fill(3);
};
</script>

<style>
.container {
  text-align: center;
}

.fixed-card {
  width: 600px;
  height: 200px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.question-text {
  min-height: 60px; /* Setter en fast høyde for spørsmålsteksten */
  display: flex;
  align-items: center;
  justify-content: center;
  text-align: center;
}
</style>