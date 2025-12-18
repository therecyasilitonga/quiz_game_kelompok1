<template>
  <div class="home-wrapper">
    <!-- Cute floating particles -->
    <div class="bubble-layer"></div>
    <div class="bubble-layer2"></div>
    <div class="bubble-layer3"></div>

    <main class="content" aria-live="polite">
      <h1 class="title">Welcome to QUIZ!</h1>
      <p class="subtitle">FUN VOCABULARY LEARNING QUIZ — Ready to boost your skills?</p>

      <div class="controls">
        <button
          v-if="!countdownRunning"
          @click="beginCountdown"
          class="start-btn"
        >
          Start Adventure 🚀
        </button>

        <div v-else class="countdown-area">
          <div class="countdown-bubble" :class="{ go: countdownText === 'GO!' }">
            {{ countdownText }}
          </div>

          <button class="cancel-btn" @click="cancelCountdown">Cancel</button>
        </div>
      </div>

      <p class="hint">TEKAN UNTUK MEMULAI!</p>
    </main>
  </div>
</template>

<script setup>
import { ref, onUnmounted } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

const countdownValues = [3, 2, 1, "GO!"];
const countdownIndex = ref(0);
const countdownRunning = ref(false);
const countdownText = ref("");
let countdownTimer = null;

/* ===== SOUND EFFECTS ===== */

function playTick() {
  try {
    const ctx = new AudioContext();
    const osc = ctx.createOscillator();
    const gain = ctx.createGain();

    osc.type = "square";
    osc.frequency.value = 600;

    osc.connect(gain);
    gain.connect(ctx.destination);

    gain.gain.setValueAtTime(0.05, ctx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.1);

    osc.start();
    osc.stop(ctx.currentTime + 0.1);
  } catch {}
}

function playGoSound() {
  try {
    const ctx = new AudioContext();
    const o1 = ctx.createOscillator();
    const g = ctx.createGain();

    o1.type = "triangle";
    o1.frequency.value = 850;

    o1.connect(g);
    g.connect(ctx.destination);

    g.gain.setValueAtTime(0.05, ctx.currentTime);
    g.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.2);

    o1.start();
    o1.stop(ctx.currentTime + 0.25);
  } catch {}
}

/* ===== COUNTDOWN LOGIC ===== */

function beginCountdown() {
  countdownRunning.value = true;
  countdownIndex.value = 0;
  countdownText.value = countdownValues[0];
  playTick();

  countdownTimer = setInterval(() => {
    countdownIndex.value++;
    if (countdownIndex.value < countdownValues.length) {
      countdownText.value = countdownValues[countdownIndex.value];
      countdownText.value === "GO!" ? playGoSound() : playTick();
    } else {
      stopCountdown();
      setTimeout(() => router.push("/PlayingGame"), 350);
    }
  }, 900);
}

function stopCountdown() {
  clearInterval(countdownTimer);
  countdownTimer = null;
  countdownRunning.value = false;
  countdownIndex.value = 0;
  countdownText.value = "";
}

function cancelCountdown() {
  stopCountdown();
}

onUnmounted(stopCountdown);
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Nunito:wght@600;700;900&display=swap');

.home-wrapper {
  width: 100%;
  height: calc(100vh - var(--navbar-height, 0px) - var(--footer-height, 0px));
  background: linear-gradient(180deg, #081a33, #020c1b, #010814);
  position: relative;
  overflow: hidden;

  display: flex;
  align-items: center;
  justify-content: center;

  color: #e6f6ff;
  font-family: "Nunito", sans-serif;
}

/* PARTICLE BACKGROUND */
.bubble-layer,
.bubble-layer2,
.bubble-layer3 {
  position: absolute;
  width: 200%;
  height: 200%;
  background: radial-gradient(circle, rgba(120,200,255,0.14) 1px, transparent 2px);
  animation: float 120s linear infinite;
}

.bubble-layer2 {
  opacity: 0.12;
  animation-duration: 160s;
}

.bubble-layer3 {
  opacity: 0.07;
  animation-duration: 200s;
}

@keyframes float {
  from { transform: translateY(0); }
  to { transform: translateY(-900px); }
}

/* MAIN CONTENT */
.content {
  text-align: center;
  padding: 1.2rem;
  z-index: 10;
}

.title {
  font-size: 2.2rem;
  font-weight: 900;
  color: #bfe9ff;
  text-shadow: 0 0 24px rgba(120,200,255,0.65);
}

.subtitle {
  margin-top: 8px;
  font-size: 1rem;
  color: #9fdcff;
  opacity: 0.95;
}

/* START BUTTON */
.start-btn {
  margin-top: 1.5rem;
  background: linear-gradient(45deg, #38bdf8, #0ea5e9);
  color: #021b2d;

  padding: 1rem 2.8rem;
  border-radius: 18px;

  font-size: 1rem;
  font-weight: 900;

  border: none;
  cursor: pointer;
  transition: 0.2s ease;

  box-shadow:
    0 0 20px rgba(56,189,248,0.75),
    inset 0 0 10px rgba(255,255,255,0.25);
}

.start-btn:hover {
  transform: scale(1.06);
  box-shadow:
    0 0 32px rgba(56,189,248,0.95),
    inset 0 0 14px rgba(255,255,255,0.35);
}

/* COUNTDOWN */
.countdown-area {
  margin-top: 1.2rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 14px;
}

.countdown-bubble {
  width: 210px;
  height: 210px;
  border-radius: 50%;

  background: linear-gradient(180deg, #e0f4ff, #b6e6ff);
  color: #042338;

  display: flex;
  justify-content: center;
  align-items: center;

  font-size: 3.5rem;
  font-weight: 900;

  box-shadow: 0 0 42px rgba(120,200,255,0.65);
  transition: 0.25s ease;
}

/* GO EFFECT */
.countdown-bubble.go {
  background: linear-gradient(45deg, #38bdf8, #0ea5e9);
  color: #ffffff;
  font-size: 2.6rem;
  animation: pop 0.45s ease;
  box-shadow: 0 0 65px rgba(56,189,248,0.95);
}

@keyframes pop {
  0% { transform: scale(0.7); }
  50% { transform: scale(1.3); }
  100% { transform: scale(1); }
}

/* CANCEL BUTTON */
.cancel-btn {
  background: rgba(0,0,0,0.45);
  padding: 10px 24px;
  border: 2px solid #38bdf8;
  border-radius: 10px;

  font-size: 0.9rem;
  color: #d6f2ff;

  cursor: pointer;
  transition: 0.15s ease;
}

.cancel-btn:hover {
  background: rgba(0,0,0,0.7);
}

/* HINT */
.hint {
  margin-top: 14px;
  font-size: 0.85rem;
  color: #bfe9ff;
  opacity: 0.9;
}
</style>
