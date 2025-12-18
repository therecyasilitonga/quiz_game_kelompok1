<template>
  <div class="landing-content-wrapper">
    <main class="main-content">
      <h1>Fun Vocabulary Learning Quiz</h1>

      <p class="subtitle">
        Belajar kosakata bahasa Inggris dengan cara seru, interaktif,
        dan penuh tantangan!
      </p>

      <div class="buttons">
        <button class="register-button" @click="goRegister">
          📝 Register
        </button>
        <button class="login-button" @click="goLogin">
          ▶ Login Sebagai Pemain
        </button>
      </div>
    </main>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'

const router = useRouter()

const goLogin = () => {
  router.push('/login')
}

const goRegister = () => {
  router.push('/register')
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

/* =============================
   LAYOUT
============================= */
.landing-content-wrapper {
  min-height: calc(100vh - var(--navbar-height, 0px) - var(--footer-height, 0px));
  display: flex;
  flex-direction: column;
  background: radial-gradient(circle at top, #0f1a3a, #070b18);
  color: #ffffff;
  font-family: 'Press Start 2P', monospace;
}

.main-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 3rem 1rem;
}

/* =============================
   TEXT
============================= */
.main-content h1 {
  font-size: 1.2rem;
  margin-bottom: 1.5rem;
  color: #3b82f6;
  text-shadow: 
    0 0 6px rgba(59, 130, 246, 0.8),
    0 0 14px rgba(96, 165, 250, 0.6);
}

.subtitle {
  font-size: 0.75rem;
  max-width: 520px;
  margin-bottom: 2.5rem;
  line-height: 1.8;
  color: #c7d2fe;
}

/* =============================
   BUTTONS
============================= */
.buttons {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  justify-content: center;
}

.register-button,
.login-button {
  background-color: rgba(10, 15, 40, 0.8);
  border: 2px solid #3b82f6;
  color: #93c5fd;
  padding: 0.75rem 1.2rem;
  font-family: 'Press Start 2P', monospace;
  cursor: pointer;
  transition: all 0.25s ease;
  white-space: nowrap;
  box-shadow: 0 0 10px rgba(59, 130, 246, 0.3);
}

.register-button:hover,
.login-button:hover {
  background-color: #3b82f6;
  color: #070b18;
  transform: translateY(-2px);
  box-shadow:
    0 0 12px rgba(96, 165, 250, 0.9),
    0 0 24px rgba(59, 130, 246, 0.7);
}

.register-button:active,
.login-button:active {
  transform: scale(0.95);
}

/* =============================
   RESPONSIVE
============================= */
@media (max-width: 768px) {
  .main-content h1 {
    font-size: 1rem;
  }

  .subtitle {
    font-size: 0.7rem;
  }

  .register-button,
  .login-button {
    font-size: 0.7rem;
    padding: 0.6rem 1rem;
  }
}

@media (max-width: 480px) {
  .buttons {
    flex-direction: column;
    width: 85%;
  }

  .register-button,
  .login-button {
    width: 100%;
  }
}
</style>
