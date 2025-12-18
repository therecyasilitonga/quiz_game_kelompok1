<template>
  <PersonalitySystem ref="pSystem" @done="savePersonality" />

  <div class="playing-game-content-wrapper">

    <!-- COUNTDOWN -->
    <div v-if="showCountdown" class="countdown-screen">
      <div class="countdown-inner">
        <div v-if="countdown > 0" class="countdown-number">
          {{ countdown }}
        </div>
        <div v-else class="countdown-number go">GO!</div>
      </div>
    </div>

    <main class="game-content">

      <!-- ===== MENU ===== -->
      <section v-if="gameState === 'menu'" class="menu-screen">
        <h1 class="title">Mini Games Hub</h1>
        <p class="subtitle">Choose a game to play</p>

        <div class="menu-grid">
          <button class="menu-card" @click="startGame('vocab')">
            🐾 Vocabulary Quiz
          </button>

          <button class="menu-card" @click="startGame('color')">
            ✍️ Simple Past Tense
          </button>

          <button class="menu-card" @click="startGame('logic')">
            🧩 Logic Puzzle
          </button>
        </div>
      </section>

      <!-- ===== VOCAB QUIZ ===== -->
      <section v-if="gameState === 'vocab'" class="game-screen">
        <header class="game-header">
          <button @click="backToMenu">← Back</button>
          <span>Score: {{ vocabScore }} | ⏱ {{ timeLeft }}s</span>
        </header>

        <p class="q-index">
          Question {{ currentVocabIndex + 1 }} / {{ vocabQuestions.length }}
        </p>

        <h3 class="q-text">{{ currentVocab.question }}</h3>
        <p class="clue">{{ currentVocab.clue }}</p>

        <div class="options-container">
          <button
            v-for="(opt, i) in currentVocab.choices"
            :key="i"
            :disabled="isSubmitting"
            @click="answerVocab(i)"
          >
            {{ optionLabel(i) }}. {{ opt }}
          </button>
        </div>

        <p v-if="vocabMessage" :class="vocabMessageType">
          {{ vocabMessage }}
        </p>
      </section>

      <!-- ===== GRAMMAR QUIZ ===== -->
      <section v-if="gameState === 'color'" class="game-screen">
        <header class="game-header">
          <button @click="backToMenu">← Back</button>
          <span>Score: {{ colorScore }} | ⏱ {{ timeLeft }}s</span>
        </header>

        <p class="q-index">
          Question {{ currentGrammarIndex + 1 }} / {{ grammarQuestions.length }}
        </p>

        <h3 class="q-text">{{ currentGrammar.question }}</h3>

        <div class="options-container">
          <button
            v-for="(opt, i) in currentGrammar.choices"
            :key="i"
            :disabled="isGrammarSubmitting"
            @click="answerGrammar(i)"
          >
            {{ optionLabel(i) }}. {{ opt }}
          </button>
        </div>

        <p v-if="grammarMessage" :class="grammarMessageType">
          {{ grammarMessage }}
        </p>
      </section>

      <!-- ===== LOGIC PUZZLE ===== -->
      <section v-if="gameState === 'logic'" class="game-screen">
        <header class="game-header">
          <button @click="backToMenu">← Back</button>
          <span>Score: {{ logicScore }} | ⏱ {{ timeLeft }}s</span>
        </header>

        <p class="logic-question">{{ currentLogic.question }}</p>

        <input
          v-model="logicAnswer"
          class="game-input"
          placeholder="Type your answer"
          @keyup.enter="checkLogicAnswer"
        />

        <div class="code-actions">
          <button @click="checkLogicAnswer">Submit</button>
          <button @click="nextLogic">Next</button>
        </div>

        <p v-if="logicMessage" :class="logicMessageType">
          {{ logicMessage }}
        </p>
      </section>

      <!-- ===== GAME OVER ===== -->
      <section v-if="gameState === 'game_over'" class="menu-screen">
        <h2>🏁 Game Over</h2>
        <p>{{ lastResult }}</p>

        <p>Vocabulary: {{ vocabScore }}</p>
        <p>Grammar: {{ colorScore }}</p>
        <p>Logic: {{ logicScore }}</p>

        <button @click="resetAll">Back to Menu</button>
      </section>

    </main>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

/* ================= STATE ================= */
const gameState = ref('menu')

/* ===== COUNTDOWN ===== */
const countdown = ref(0)
const showCountdown = ref(false)

/* ===== TIMER ===== */
const timeLeft = ref(10)
let answerTimer = null

function startAnswerTimer(onTimeUp) {
  clearInterval(answerTimer)
  timeLeft.value = 10
  answerTimer = setInterval(() => {
    timeLeft.value--
    if (timeLeft.value <= 0) {
      clearInterval(answerTimer)
      onTimeUp && onTimeUp()
    }
  }, 1000)
}
function stopAnswerTimer() {
  clearInterval(answerTimer)
}

/* ===== SOUND ===== */
function playBeep(freq = 500, dur = 0.15, type = 'sine') {
  const ctx = new AudioContext()
  const osc = ctx.createOscillator()
  const gain = ctx.createGain()
  osc.type = type
  osc.frequency.value = freq
  gain.gain.value = 0.15
  osc.connect(gain)
  gain.connect(ctx.destination)
  osc.start()
  osc.stop(ctx.currentTime + dur)
}
const soundCorrect = () => playBeep(800, 0.2, 'square')
const soundWrong = () => playBeep(200, 0.3, 'sawtooth')
const soundClick = () => playBeep(350, 0.1)
const soundCountdown = () => playBeep(300, 0.12)

/* ===== COUNTDOWN START ===== */
function startCountdown(cb) {
  showCountdown.value = true
  countdown.value = 3
  const t = setInterval(() => {
    soundCountdown()
    countdown.value--
    if (countdown.value < 0) {
      clearInterval(t)
      showCountdown.value = false
      playBeep(1000, 0.2)
      cb && cb()
    }
  }, 1000)
}

/* ===== NAV ===== */
function startGame(id) {
  soundClick()
  startCountdown(() => {
    if (id === 'vocab') resetVocab()
    if (id === 'color') resetColor()
    if (id === 'logic') resetLogic()
    gameState.value = id
  })
}
function backToMenu() {
  stopAnswerTimer()
  gameState.value = 'menu'
}

/* ================= VOCAB ================= */
const vocabQuestions = [
  {
    question: "What is the meaning of 'jungle'?",
    clue: 'A place full of plants and animals.',
    choices: [
      'A large desert',
      'A thick forest with many plants and animals',
      'A dry grassland',
      'A snowy mountain'
    ],
    answerIndex: 1
  },
  {
    question: "What is a 'vine'?",
    clue: 'It climbs.',
    choices: [
      'A small rock',
      'A long climbing plant',
      'A type of insect',
      'A large tree stump'
    ],
    answerIndex: 1
  },
  {
    question: "The word 'predator' refers to…",
    clue: 'It hunts.',
    choices: [
      'An animal that eats plants',
      'An animal that hunts other animals',
      'A baby animal',
      'A slow-moving reptile'
    ],
    answerIndex: 1
  },
  {
    question: "What is the meaning of 'wildlife'?",
    clue: 'Free animals.',
    choices: [
      'Animals kept in zoos',
      'Animals living freely in nature',
      'Farm animals',
      'Pets'
    ],
    answerIndex: 1
  },
  {
    question: "What is a 'canopy' in the jungle?",
    clue: 'A natural roof.',
    choices: [
      'The ground covered with leaves',
      'A cave where tigers live',
      'The top layer of the trees forming a roof',
      'A place with many rivers'
    ],
    answerIndex: 2
  },
  {
    question: "“Endangered” animals are animals that are…",
    clue: 'In danger.',
    choices: [
      'Very common',
      'Newly discovered',
      'At risk of extinction',
      'Safe in the jungle'
    ],
    answerIndex: 2
  },
  {
    question: "What does the word 'riverbank' mean?",
    clue: 'Beside the water.',
    choices: [
      'A bank for storing money',
      'The land along the side of a river',
      'A deep part of the river',
      'A waterfall'
    ],
    answerIndex: 1
  },
  {
    question: "The word 'habitat' means…",
    clue: 'Natural home.',
    choices: [
      'A group of animals',
      'A type of weather',
      'The natural home of a plant or animal',
      'A jungle tool'
    ],
    answerIndex: 2
  },
  {
    question: "What is a 'rainforest'?",
    clue: 'Rains a lot.',
    choices: [
      'A forest with no trees',
      'A forest covered in snow',
      'A forest with heavy rainfall and many species',
      'A forest made of bamboo'
    ],
    answerIndex: 2
  },
  {
    question: "'Claws' are…",
    clue: 'Sharp.',
    choices: [
      'Animal ears',
      'Animal tails',
      'Sharp nails of animals',
      'Animal noses'
    ],
    answerIndex: 2
  },
  {
    question: "What does 'camouflage' mean?",
    clue: 'Hide by blending.',
    choices: [
      'To run very fast',
      'To sleep during the day',
      'To blend into the environment to hide',
      'To make loud sounds'
    ],
    answerIndex: 2
  },
  {
    question: "A 'tribe' refers to…",
    clue: 'People group.',
    choices: [
      'A group of trees',
      'A group of people with the same culture',
      'A large animal',
      'A small hut in the jungle'
    ],
    answerIndex: 1
  },
  {
    question: "The word 'swamp' means…",
    clue: 'Muddy wetland.',
    choices: [
      'A rocky hill',
      'Wet, muddy land with water',
      'A dry valley',
      'A desert area'
    ],
    answerIndex: 1
  },
  {
    question: "'Poacher' means…",
    clue: 'Illegal hunter.',
    choices: [
      'A forest farmer',
      'A person who studies plants',
      'A person who hunts animals illegally',
      'A jungle guide'
    ],
    answerIndex: 2
  },
  {
    question: "What is the meaning of 'creature'?",
    clue: 'Any animal.',
    choices: [
      'A type of plant',
      'Any living animal',
      'A piece of wood',
      'A rock in the river'
    ],
    answerIndex: 1
  }
]

const currentVocabIndex = ref(0)
const vocabScore = ref(0)
const vocabMessage = ref('')
const vocabMessageType = ref('')
const isSubmitting = ref(false)

const currentVocab = computed(() => vocabQuestions[currentVocabIndex.value])

function optionLabel(i) {
  return String.fromCharCode(65 + i)
}

function resetVocab() {
  currentVocabIndex.value = 0
  vocabScore.value = 0
  startAnswerTimer(() => nextVocab())
}

function answerVocab(i) {
  stopAnswerTimer()
  if (isSubmitting.value) return
  isSubmitting.value = true

  if (i === currentVocab.value.answerIndex) {
    vocabScore.value += 10
    soundCorrect()
    vocabMessage.value = 'Correct!'
    vocabMessageType.value = 'success'
  } else {
    soundWrong()
    vocabMessage.value = 'Wrong!'
    vocabMessageType.value = 'error'
  }

  setTimeout(() => {
    vocabMessage.value = ''
    isSubmitting.value = false
    nextVocab()
  }, 700)
}

function nextVocab() {
  if (currentVocabIndex.value < vocabQuestions.length - 1) {
    currentVocabIndex.value++
    startAnswerTimer(() => nextVocab())
  } else {
    lastResult.value = 'Vocabulary Quiz Completed'
    gameState.value = 'game_over'
  }
}

/* ---------------------------
   GRAMMAR QUIZ – SIMPLE PAST
--------------------------- */

const grammarQuestions = [
  {
    question: "Yesterday, I ___ (go) to the market with my mom.",
    choices: ["go", "goed", "went", "gone"],
    answerIndex: 2
  },
  {
    question: "She ___ (eat) noodles for breakfast this morning.",
    choices: ["eat", "eated", "ate", "eaten"],
    answerIndex: 2
  },
  {
    question: "We ___ (see) a beautiful rainbow after the rain.",
    choices: ["see", "saw", "seen", "seed"],
    answerIndex: 1
  },
  {
    question: "My father ___ (buy) a new helmet last week.",
    choices: ["buy", "buyed", "bought", "buys"],
    answerIndex: 2
  },
  {
    question: "They ___ (make) a cake for the school event.",
    choices: ["make", "made", "maked", "making"],
    answerIndex: 1
  },
  {
    question: "I ___ (bring) my laptop to class yesterday.",
    choices: ["bring", "brought", "bringed", "brung"],
    answerIndex: 1
  },
  {
    question: "He ___ (speak) English during the presentation.",
    choices: ["speak", "spoke", "speaked", "spoken"],
    answerIndex: 1
  },
  {
    question: "Tasya ___ (write) a letter to her friend.",
    choices: ["write", "wrote", "written", "writed"],
    answerIndex: 1
  },
  {
    question: "The students ___ (take) many photos during the trip.",
    choices: ["take", "taken", "taked", "took"],
    answerIndex: 3
  },
  {
    question: "My cousin ___ (come) to my house last night.",
    choices: ["come", "comed", "came", "comes"],
    answerIndex: 2
  }
]

const currentGrammarIndex = ref(0)
const colorScore = ref(0)
const grammarMessage = ref('')
const grammarMessageType = ref('')
const isGrammarSubmitting = ref(false)

const currentGrammar = computed(() => grammarQuestions[currentGrammarIndex.value])

function resetColor() {
  currentGrammarIndex.value = 0
  colorScore.value = 0
  startAnswerTimer(() => nextGrammar())
}

function answerGrammar(i) {
  stopAnswerTimer()
  if (isGrammarSubmitting.value) return
  isGrammarSubmitting.value = true

  if (i === currentGrammar.value.answerIndex) {
    colorScore.value += 10
    soundCorrect()
    grammarMessage.value = 'Correct!'
    grammarMessageType.value = 'success'
  } else {
    soundWrong()
    grammarMessage.value = 'Wrong!'
    grammarMessageType.value = 'error'
  }

  setTimeout(() => {
    grammarMessage.value = ''
    isGrammarSubmitting.value = false
    nextGrammar()
  }, 700)
}

function nextGrammar() {
  if (currentGrammarIndex.value < grammarQuestions.length - 1) {
    currentGrammarIndex.value++
    startAnswerTimer(() => nextGrammar())
  } else {
    lastResult.value = 'Grammar Quiz Completed'
    gameState.value = 'game_over'
  }
}

/* ================= LOGIC ================= */
const logicQuestions = [
  // WORD SCRAMBLE (1–10)
  { question: 'Unscramble: JREOPTOCR', answer: 'projector' },
  { question: 'Unscramble: BRECOLDIPA', answer: 'clipboard' },
  { question: 'Unscramble: GHHLIRETGI', answer: 'highlighter' },
  { question: 'Unscramble: PRETACSOLRO', answer: 'protractor' },
  { question: 'Unscramble: CLIPDBRINE', answer: 'binderclip' },
  { question: 'Unscramble: EWROTHIBAD', answer: 'whiteboard' },
  { question: 'Unscramble: MPAOSCS', answer: 'compass' },
  { question: 'Unscramble: RTAKEM', answer: 'marker' },
  { question: 'Unscramble: OBKNOTEO', answer: 'notebook' },
  { question: 'Unscramble: TLNIAMARO', answer: 'laminator' },

  // RIDDLE PUZZLE (11–20)
  { question: 'I help you draw perfect circles. What am I?', answer: 'compass' },
  { question: 'You use me to highlight important information. Who am I?', answer: 'highlighter' },
  { question: 'I hold pieces of paper together without glue. What am I?', answer: 'binderclip' },
  { question: 'Teachers point at the screen using my red light. Who am I?', answer: 'laser pointer' },
  { question: 'You write on me using markers, not chalk. What am I?', answer: 'whiteboard' },
  { question: 'I can punch holes in paper. Who am I?', answer: 'hole puncher' },
  { question: 'I protect paper with a plastic layer. Who am I?', answer: 'laminator' },
  { question: 'Students write notes in me. Who am I?', answer: 'notebook' },
  { question: 'I keep papers neat on a table. What am I?', answer: 'document tray' },
  { question: 'I erase pencil marks. Who am I?', answer: 'eraser' }
]

const currentLogicIndex = ref(0)
const logicAnswer = ref('')
const logicMessage = ref('')
const logicMessageType = ref('')
const logicScore = ref(0)

const currentLogic = computed(() => logicQuestions[currentLogicIndex.value])

function resetLogic() {
  currentLogicIndex.value = 0
  logicScore.value = 0
  startAnswerTimer(() => soundWrong())
}

function checkLogicAnswer() {
  stopAnswerTimer()
  if (logicAnswer.value.toLowerCase() === currentLogic.value.answer) {
    logicScore.value += 5
    soundCorrect()
    logicMessage.value = 'Correct!'
    logicMessageType.value = 'success'
  } else {
    soundWrong()
    logicMessage.value = `Wrong: ${currentLogic.value.answer}`
    logicMessageType.value = 'error'
  }
}

function nextLogic() {
  logicAnswer.value = ''
  logicMessage.value = ''
  if (currentLogicIndex.value < logicQuestions.length - 1) {
    currentLogicIndex.value++
    startAnswerTimer(() => {})
  } else {
    lastResult.value = 'Logic Puzzle Completed'
    gameState.value = 'game_over'
  }
}

/* ================= GAME OVER ================= */
const lastResult = ref('')

function resetAll() {
  stopAnswerTimer()
  gameState.value = 'menu'
}
</script>


<style scoped>
/* ---- Theme ---- */
:root {
  --bg: #050b18;
  --panel: #3bbcff;
  --accent: #1e6bff;
  --accent-soft: rgba(30, 107, 255, 0.25);
  --muted: #9fb6ff;
  --glass: rgba(255,255,255,0.08);
  --radius: 14px;
  --card-bg: rgba(255,255,255,0.06);
  --transition: 0.22s cubic-bezier(.25,.46,.45,.94);
}


/* ===== FIX CARD VISIBILITY ===== */
.menu-card {
  background: rgba(255, 255, 255, 0.08); /* lebih terang */
  border: 1px solid rgba(255,255,255,0.14);
  color: #f4fff7; /* teks jadi putih */
}

/* Hover lebih bersih */
.menu-card:hover {
  background: rgba(19, 3, 247, 0.12);
  border-color: var(--accent);
  transform: translateY(-6px) scale(1.02);
}

/* ===== FIX TITLE ===== */
.title {
  color: #ffffff;
  font-size: 2.8rem;
  font-weight: 800;
  text-shadow: 0 0 12px rgba(6, 26, 247, 0.35);
}

.subtitle {
  color: #d6f5dd;
  font-size: 1.15rem;
  margin-top: 6px;
}

/* ===== FIX ICON IN CARD ===== */
.card-icon {
  font-size: 2.4rem;
  color: var(--accent);
  filter: drop-shadow(0 0 6px var(--accent-soft));
}

/* ===== TEXT INSIDE CARD ===== */
.menu-card h2 {
  font-size: 1.65rem;
  color: #eaffef;
  margin-bottom: 4px;
}

.menu-card p {
  font-size: 0.95rem;
  color: #c8e6d3;
}

/* ===== CARD GRID ===== */
.menu-grid {
  gap: 26px;
  padding: 0 12px;
  margin-top: 40px;
}

/* ===== PAGE CENTER FIX ===== */
.playing-game-content-wrapper {
  padding-top: 80px;
}

/* ===== MOBILE RESPONSIVE ===== */
@media (max-width: 720px) {
  .menu-grid {
    grid-template-columns: 1fr;
  }
  .title {
    font-size: 2.2rem;
  }
  .menu-card {
    padding: 20px;
  }
  .card-icon {
    font-size: 2.2rem;
  }
}


/* ---- Main Wrapper ---- */
/* ---- Main Wrapper ---- */
.playing-game-content-wrapper {
  min-height: 100vh;
  background: radial-gradient(
    circle at 20% 20%,
    #0b2a6f 0%,
    #040a1a 100%
  );
  color: #eaf2ff;
  font-family: Inter, system-ui, "Segoe UI", Roboto, Arial;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 48px 20px;
}


/* ---- Countdown Overlay ---- */
.countdown-screen {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.85);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 9999;
}

.countdown-inner {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 10px;
}

.countdown-number {
  font-size: 5.2rem;
  font-weight: 900;
  color: var(--accent);
  text-shadow: 0 0 28px var(--accent-soft);
}

.countdown-number.go {
  font-size: 4.2rem;
}

/* ---- Content ---- */
.game-content {
  width: 100%;
  max-width: 980px;
  backdrop-filter: blur(12px);

  /* === TAMBAHAN (visual saja) === */
  background: rgba(255, 255, 255, 0.06); /* lebih muda dari background */
  border-radius: 18px;
  padding: 48px 36px;
  border: 1px solid rgba(255, 255, 255, 0.12);
  box-shadow: 0 18px 50px rgba(0, 0, 0, 0.45);
}

/* ---- Menu ---- */
.menu-screen {
  text-align: center;
}


.title {
  font-size: 2.6rem;
  margin-bottom: .3rem;
  color: var(--accent);
  font-weight: 700;
  text-shadow: 0 0 18px var(--accent-soft);
}

.subtitle {
  color: var(--muted);
  font-size: 1.05rem;
}

.menu-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 18px;
  margin-top: 24px;
}

.menu-card {
  background: var(--card-bg);
  border: 1px solid var(--accent-soft);
  padding: 24px;
  border-radius: var(--radius);
  cursor: pointer;
  text-align: left;
  transition: var(--transition);
  display: flex;
  flex-direction: column;
  gap: 10px;
  backdrop-filter: blur(8px);
  box-shadow: 0 4px 18px rgba(0,0,0,0.35);
}

.menu-card:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 0 12px 35px rgba(0,0,0,0.65);
  border-color: var(--accent);
}

.card-icon {
  font-size: 1.9rem;
  color: var(--accent);
}

/* ---- Game Screen ---- */
.game-screen {
  background: var(--card-bg);
  padding: 24px;
  border-radius: var(--radius);
  border: 1px solid var(--accent-soft);
  backdrop-filter: blur(10px);
  box-shadow: 0 6px 20px rgba(0,0,0,0.4);
}

.game-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  margin-bottom: 20px;
}

.back-btn {
  background: var(--glass);
  border: 1px solid rgba(255,255,255,0.08);
  color: var(--accent);
  padding: 8px 14px;
  border-radius: var(--radius);
  cursor: pointer;
  transition: var(--transition);
}

.back-btn:hover {
  border-color: var(--accent);
  background: rgba(0, 59, 251, 0.08);
}

.score {
  color: var(--accent);
  font-weight: 700;
  font-size: 1.05rem;
}

/* ---- Quiz ---- */
.quiz-box {
  background: rgba(0,0,0,0.4);
  padding: 20px;
  border-radius: 12px;
  border: 1px solid rgba(255,255,255,0.04);
  backdrop-filter: blur(6px);
}

.q-index {
  font-size: .9rem;
  color: var(--muted);
  margin-bottom: 8px;
}

.q-text {
  color: var(--accent);
  margin-bottom: 10px;
  font-size: 1.1rem;
}

.clue {
  font-size: .95rem;
  color: var(--muted);
  margin-bottom: 16px;
}

.options-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
  margin-bottom: 16px;
}

.option {
  display: flex;
  gap: 12px;
  align-items: center;
  background: rgba(255,255,255,0.04);
  border: 2px solid var(--accent-soft);
  color: #dfffe0;
  padding: 14px;
  border-radius: 10px;
  cursor: pointer;
  transition: var(--transition);
  text-align: left;
  font-size: .98rem;
}

.option:hover {
  transform: translateY(-6px);
  background: rgba(2, 2, 52, 0.1);
  border-color: var(--accent);
  color: #0151ff;
}

.opt-label {
  font-weight: 800;
  color: var(--accent);
}

/* ---- Inputs & Buttons ---- */
.game-input {
  width: 100%;
  padding: 12px 14px;
  border-radius: var(--radius);
  border: 1px solid rgba(255,255,255,0.06);
  background: rgba(255,255,255,0.05);
  color: #eaffea;
  margin-bottom: 12px;
  transition: var(--transition);
}

.game-input:focus {
  outline: none;
  border-color: var(--accent);
  background: rgba(2, 25, 83, 0.08);
}

.code-actions, .color-btns {
  display: flex;
  gap: 12px;
  margin-bottom: 10px;
}

.submit-btn, .next-btn, .skip-btn {
  padding: 10px 16px;
  border-radius: var(--radius);
  cursor: pointer;
  font-weight: 700;
  font-size: .95rem;
  transition: var(--transition);
}

.submit-btn {
  background: var(--accent);
  color: #021ffd;
}

.submit-btn:hover {
  filter: brightness(1.15);
}

.next-btn, .skip-btn {
  background: transparent;
  border: 1px solid rgba(255,255,255,0.1);
  color: #e9fff0;
}

.next-btn:hover, .skip-btn:hover {
  background: rgba(255,255,255,0.06);
  border-color: var(--accent);
}

/* ---- Feedback ---- */
.feedback {
  margin-top: 10px;
  padding: 8px 12px;
  border-radius: var(--radius);
  font-weight: 700;
  font-size: .95rem;
}

.feedback.success {
  background: rgba(3, 44, 250, 0.12);
  color: var(--accent);
  border: 1px solid var(--accent-soft);
}

.feedback.error {
  background: rgba(255,0,0,0.08);
  color: #ff9e9e;
  border: 1px solid rgba(255,0,0,0.2);
}

/* ---- Color Box ---- */
.color-box-area {
  display: flex;
  gap: 18px;
  align-items: center;
  justify-content: center;
  margin-top: 14px;
  flex-wrap: wrap;
}

.color-box {
  width: 240px;
  height: 170px;
  border-radius: var(--radius);
  border: 3px solid rgba(255,255,255,0.08);
  box-shadow: inset 0 0 40px rgba(0,0,0,0.45);
  transition: var(--transition);
}

.color-box:hover {
  transform: scale(1.03);
}

/* ---- Logic Box ---- */
.logic-box {
  background: rgba(0,0,0,0.4);
  padding: 20px;
  border-radius: 12px;
  border: 1px solid rgba(255,255,255,0.04);
}

/* ---- Responsive ---- */
@media (max-width: 720px) {
  .options-container {
    grid-template-columns: 1fr;
  }
  .menu-grid {
    grid-template-columns: 1fr;
  }
  .color-box {
    width: 100%;
    height: 150px;
  }
  .game-content {
    padding: 12px;
  }
  .title {
    font-size: 2.2rem;
  }
}
</style>
