<template>
  <div class="game-over-content-wrapper">
    <main class="main-content">

      <!-- ===== TITLE ===== -->
      <h1
        class="game-over-title"
        :class="{ 'win-state': grade === 'A' }"
      >
        {{ titleText }}
      </h1>

      <!-- ===== MAIN MESSAGE ===== -->
      <p class="game-over-message">
        {{ mainMessage }}
      </p>

      <!-- ===== SCORE SUMMARY ===== -->
      <div class="score-summary">
        <h2>📊 Ringkasan Hasil</h2>

        <p>
          Skor Akhir:
          <span class="highlight-score">{{ finalScore }}</span>
        </p>

        <p>
          Nilai:
          <span class="grade">{{ grade }}</span>
        </p>

        <p>Total Pertanyaan: {{ totalQuestions }}</p>
      </div>

      <!-- ===== MOTIVATION ===== -->
      <div class="encouragement-box">
        <h3>{{ motivationTitle }}</h3>
        <p v-for="(line, i) in motivationText" :key="i">
          {{ line }}
        </p>
      </div>

      <!-- ===== ACTION BUTTONS ===== -->
      <div class="action-buttons">
        <button class="play-again-button" @click="playAgain">
          🎮 Main Lagi
        </button>

        <button class="learning-button" @click="goToLearning">
          📘 Ke Menu Pembelajaran
        </button>
      </div>

    </main>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

/* ================= DATA ================= */
const finalScore = ref(0);
const totalQuestions = ref(0);

/* ================= LOAD RESULT ================= */
onMounted(() => {
  const stored = localStorage.getItem("gameResult");
  if (stored) {
    const data = JSON.parse(stored);
    finalScore.value = data.finalScore || 0;
    totalQuestions.value = data.totalQuestions || 0;
  }
});

/* ================= GRADE ================= */
const grade = computed(() => {
  if (finalScore.value >= 85) return "A";
  if (finalScore.value >= 60) return "B";
  return "C";
});

/* ================= TEXT LOGIC ================= */
const titleText = computed(() => {
  if (grade.value === "A") return "🌟 LUAR BIASA!";
  if (grade.value === "B") return "👍 KERJA BAGUS!";
  return "💪 JANGAN MENYERAH!";
});

const mainMessage = computed(() => {
  if (grade.value === "A") {
    return "Kamu berhasil menguasai dasar Bahasa Inggris dengan sangat baik.";
  }
  if (grade.value === "B") {
    return "Pemahamanmu sudah cukup kuat, tinggal diasah sedikit lagi.";
  }
  return "Setiap kesalahan adalah bagian dari proses belajar. Kamu bisa!";
});

const motivationTitle = computed(() => {
  if (grade.value === "A") return "EXCELLENT";
  if (grade.value === "B") return "IMPROVING";
  return "Agent Level: TRAINING";
});

const motivationText = computed(() => {
  if (grade.value === "A") {
    return [
      "Kamu menunjukkan konsistensi dan fokus yang luar biasa.",
      "Teruskan belajar dan tantang dirimu ke materi berikutnya."
    ];
  }

  if (grade.value === "B") {
    return [
      "Kamu sudah berada di jalur yang benar.",
      "Sedikit pengulangan materi akan membuatmu semakin percaya diri.",
      "mahir bahasa inggris dibentuk dari latihan yang konsisten."
    ];
  }

  return [
    "Jangan berkecil hati — semua agent hebat pernah berada di posisi ini.",
    "Pelajari kembali materi dasar dengan tenang.",
    "Kekuatan terbesar mahir bahasa inggris adalah kemauan untuk belajar."
  ];
});

/* ================= ACTION ================= */
const playAgain = () => {
  localStorage.removeItem("gameResult");
  router.push("/PlayingGame");
};

const goToLearning = () => {
  localStorage.removeItem("gameResult");
  router.push("/karakter-game");
};

</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

:root {
  --neon-green: rgb(16, 59, 211);
  --dark-green: rgb(39, 122, 205);
  --dark-bg: #111;
  --card-bg: #222;
  --text-color: #eee;
  --light-text: #ccc;
}

/* ===== WRAPPER ===== */
.game-over-content-wrapper {
  background-color: var(--dark-bg);
  color: var(--text-color);
  font-family: 'Press Start 2P', monospace;
  min-height: calc(100vh - var(--navbar-height, 0px));
  display: flex;
  flex-direction: column;
  flex: 1;
  position: relative;
  overflow: hidden;
}

/* SCANLINE EFFECT */
.game-over-content-wrapper::before {
  content: '';
  position: absolute;
  inset: 0;
  background: repeating-linear-gradient(
    to bottom,
    rgba(0,0,0,0.1),
    rgba(0,0,0,0.1) 1px,
    transparent 1px,
    transparent 2px
  );
  pointer-events: none;
  opacity: 0.12;
  z-index: 10;
}

/* ===== MAIN CONTENT ===== */
.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 2rem 1rem;
  z-index: 1;
}

/* ===== CARD ===== */
.game-over-card {
  background-color: var(--card-bg);
  border: 2px solid var(--neon-green);
  padding: 2.5rem;
  border-radius: 8px;
  text-align: center;
  max-width: 600px;
  width: 100%;
  position: relative;
  box-shadow:
    0 0 20px rgba(5, 39, 235, 0.6),
    0 0 30px rgba(0, 255, 0, 0.3);
}

/* ANIMATED BORDER */
.game-over-card::before {
  content: '';
  position: absolute;
  inset: -5px;
  background: linear-gradient(
    45deg,
    var(--neon-green),
    transparent,
    var(--dark-green),
    transparent
  );
  background-size: 400% 400%;
  z-index: -1;
  filter: blur(6px);
  border-radius: 10px;
  animation: borderAnimation 8s linear infinite;
}

@keyframes borderAnimation {
  0% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
  100% { background-position: 0% 50%; }
}

/* ===== TITLE ===== */
.game-over-title {
  font-size: 2.2rem;
  margin-bottom: 1.5rem;
  color: #ff3b3b;
  text-shadow: 0 0 8px rgba(255, 0, 0, 0.8);
  animation: pulse-red 1.5s infinite alternate;
}

.game-over-title.win-state {
  color: var(--neon-green);
  text-shadow: 0 0 10px rgba(6, 124, 250, 0.9);
  animation: pulse-green 1.5s infinite alternate;
}

/* ===== MESSAGE ===== */
.game-over-message {
  font-size: 0.85rem;
  line-height: 1.7;
  color: var(--light-text);
  margin-bottom: 2rem;
}

/* ===== SCORE SUMMARY ===== */
.score-summary {
  background-color: #1a1a1a;
  border: 2px solid var(--neon-green);
  padding: 1.8rem;
  border-radius: 8px;
  margin-bottom: 2rem;
  width: 100%;
}

.score-summary h2 {
  color: var(--neon-green);
  margin-bottom: 1rem;
  text-shadow: 0 0 6px rgba(0, 76, 255, 0.6);
}

.highlight-score {
  color: var(--neon-green);
  font-size: 1.3rem;
}

.grade {
  color: #ffd700;
  font-size: 1.5rem;
  text-shadow: 0 0 6px rgba(255, 215, 0, 0.8);
}

/* ===== MOTIVATION BOX ===== */
.encouragement-box {
  background-color: #1a1a1a;
  border: 2px dashed var(--neon-green);
  padding: 1.6rem;
  border-radius: 8px;
  margin-bottom: 2rem;
  box-shadow: inset 0 0 12px rgba(0, 76, 255, 0.3);
}

.encouragement-box h3 {
  color: var(--neon-green);
  margin-bottom: 1rem;
  text-shadow: 0 0 6px rgba(0, 76, 255, 0.6);
}

.encouragement-box p {
  font-size: 0.75rem;
  line-height: 1.6;
  color: #ddd;
}

/* ===== BUTTONS ===== */
.action-buttons {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  justify-content: center;
}

.play-again-button,
.learning-button {
  background-color: var(--neon-green);
  color: var(--dark-bg);
  border: none;
  padding: 0.9rem 1.6rem;
  font-family: 'Press Start 2P', monospace;
  font-size: 0.75rem;
  cursor: pointer;
  border-radius: 4px;
  text-transform: uppercase;
  letter-spacing: 1px;
  transition: 0.3s;
  box-shadow: 0 0 10px var(--neon-green);
}

.learning-button {
  background-color: transparent;
  color: var(--neon-green);
  border: 1px solid var(--neon-green);
}

.play-again-button:hover,
.learning-button:hover {
  background-color: var(--dark-green);
  color: #fff;
  box-shadow: 0 0 20px var(--neon-green);
}

/* ===== ANIMATIONS ===== */
@keyframes pulse-red {
  from { transform: scale(1); opacity: 1; }
  to { transform: scale(1.05); opacity: 0.9; }
}

@keyframes pulse-green {
  from { transform: scale(1); opacity: 1; }
  to { transform: scale(1.05); opacity: 0.9; }
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  .game-over-card {
    padding: 2rem;
  }
  .game-over-title {
    font-size: 1.7rem;
  }
}

@media (max-width: 480px) {
  .game-over-card {
    padding: 1.5rem;
  }
  .game-over-title {
    font-size: 1.4rem;
  }
}
</style>
