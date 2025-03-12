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
      <button class="btn btn-primary mt-3" @click="calculateResult">Se resultat</button>
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

// Definerer 30 spørsmål
const questions = [
  { text: 'Hvordan jobber du i et team?', options: ['Jeg bringer nye ideer', 'Jeg samarbeider godt', 'Jeg delegerer oppgaver', 'Jeg er kreativ og løser problemer'] },
  { text: 'Hvordan tar du beslutninger?', options: ['Jeg tar raske beslutninger', 'Jeg analyserer nøye før jeg velger', 'Jeg stoler på ekspertise', 'Jeg følger en plan'] },
  { text: 'Hvordan håndterer du press?', options: ['Jeg holder meg rolig', 'Jeg presser meg selv til perfeksjon', 'Jeg tilpasser meg raskt', 'Jeg fokuserer på målet'] },
  { text: 'Hvordan foretrekker du å lære nye ting?', options: ['Gjennom erfaring', 'Ved å lese teori', 'Ved å se på andre', 'Ved å prøve og feile'] },
  { text: 'Hva motiverer deg mest?', options: ['Å lykkes i oppgavene mine', 'Å hjelpe andre', 'Å finne kreative løsninger', 'Å samarbeide'] },
  { text: 'Hvordan reagerer du på kritikk?', options: ['Jeg tar det til meg og forbedrer meg', 'Jeg blir defensiv', 'Jeg ignorerer det', 'Jeg diskuterer det'] },
  { text: 'Hva er viktigst i en jobb for deg?', options: ['Frihet til å være kreativ', 'Trygghet og stabilitet', 'Muligheten til å lede', 'Å jobbe i et godt team'] },
  { text: 'Hvordan håndterer du stress?', options: ['Jeg planlegger og prioriterer', 'Jeg jobber hardere', 'Jeg tar pauser og reflekterer', 'Jeg fokuserer på det viktigste'] },
  { text: 'Hvordan samarbeider du med andre?', options: ['Jeg tar initiativ og leder', 'Jeg støtter og hjelper andre', 'Jeg analyserer og gir innspill', 'Jeg følger en strukturert plan'] },
  { text: 'Hva gjør du når du møter et problem?', options: ['Jeg finner en kreativ løsning', 'Jeg analyserer situasjonen grundig', 'Jeg spør eksperter om råd', 'Jeg tar en beslutning raskt'] },
  { text: 'Hva slags oppgaver liker du best?', options: ['De som krever kreativitet', 'De som gir konkrete resultater', 'De som lar meg samarbeide', 'De som krever logisk tenkning'] },
  { text: 'Hvordan håndterer du en uforutsett situasjon?', options: ['Jeg improviserer', 'Jeg følger en plan', 'Jeg spør andre om råd', 'Jeg tar en rask beslutning'] },
  { text: 'Hva slags arbeidsmiljø foretrekker du?', options: ['Kreativt og dynamisk', 'Strukturert og stabilt', 'Samarbeidsorientert', 'Utfordrende og resultatfokusert'] },
  { text: 'Hvordan forholder du deg til regler?', options: ['Jeg følger dem nøye', 'Jeg tilpasser meg etter behov', 'Jeg utfordrer dem hvis nødvendig', 'Jeg ser dem som veiledning'] },
  { text: 'Hva gjør deg mest frustrert?', options: ['Mangel på kreativitet', 'Dårlig planlegging', 'Uklare mål', 'Manglende samarbeid'] },
  { text: 'Hva gir deg energi?', options: ['Å prøve noe nytt', 'Å løse en utfordring', 'Å hjelpe andre', 'Å se fremgang'] },
  { text: 'Hvordan reagerer du på endringer?', options: ['Jeg tilpasser meg raskt', 'Jeg foretrekker stabilitet', 'Jeg analyserer situasjonen først', 'Jeg ser det som en utfordring'] },
  { text: 'Hva beskriver deg best?', options: ['Kreativ og innovativ', 'Analytisk og nøyaktig', 'Samarbeidsvillig og hjelpsom', 'Målrettet og effektiv'] },
];

// Oppretter et array for brukerens svar, fylt med nullverdier
const userAnswers = ref(Array(questions.length).fill(null));

// Lagrer resultatet som en reaktiv verdi
const result = ref(null);

// Mapping fra svaralternativer til personlighetstyper
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

// Beregner resultat basert på flest valgte svar
const calculateResult = () => {
  // Oppretter et objekt for å telle antall valg per svar
  const counts = {};
  
  userAnswers.value.forEach(answer => {
    if (answer) {
      // Øker telleren for hvert valgt svar
      counts[answer] = (counts[answer] || 0) + 1;
    }
  });

  // Hvis ingen svar er valgt, ikke vis resultat
  if (Object.keys(counts).length === 0) {
    result.value = 'Ingen valg ble gjort';
    return;
  }

  // Finner de mest valgte svarene
  const maxCount = Math.max(...Object.values(counts));
  const mostChosenAnswers = Object.keys(counts).filter(answer => counts[answer] === maxCount);

  // Velger tilfeldig en av de mest valgte svarene hvis flere har like mange stemmer
  const finalAnswer = mostChosenAnswers[Math.floor(Math.random() * mostChosenAnswers.length)];

  // Henter personlighetstypen eller setter til 'Ukjent' hvis ingen valg er gjort
  result.value = personalities[finalAnswer] || 'Ukjent';
};

// Nullstiller quizen ved å tilbakestille svar og resultat
const resetQuiz = () => {
  result.value = null;
  userAnswers.value.fill(null);
};
</script>
