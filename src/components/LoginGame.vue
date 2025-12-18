<template>
  <div class="login-content-wrapper">
    <main class="main-content">
      <div class="form-card">
        <h1>Selamat datang di laman Login EBT Game</h1>
        <p class="tagline">mulai menjelajahi website ini. Belajar bersama!</p>
        <form @submit.prevent="handleLogin" class="login-form">
          <div class="form-group">
            <label for="identifier">Username atau Email</label>
            <input
              type="text"
              id="identifier"
              v-model="identifier"
              placeholder="Masukkan Username atau Email Anda"
              required
            />
          </div>
          <div class="form-group">
            <label for="password">Password</label>
            <input
              type="password"
              id="password"
              v-model="password"
              placeholder="Masukkan Password Anda"
              required
            />
          </div>
          <button type="submit" class="submit-button" :disabled="loading">
            {{ loading ? 'Memuat...' : 'Masuk' }}
          </button>
          <p v-if="error" class="error-message">{{ error }}</p>
        </form>
        <p class="forgot-password-link">
          Lupa Password? <router-link to="/reset-password">Reset di sini</router-link>
        </p>
        <p class="register-link">
          Belum punya akun? <router-link to="/register">Daftar sekarang</router-link>
        </p>
      </div>
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { useAuthStore } from '../stores/auth';

const router = useRouter();
const auth = useAuthStore();

const identifier = ref('');
const password = ref('');
const loading = ref(false);
const error = ref(null);

const handleLogin = async () => {
  loading.value = true;
  error.value = null;

  try {
    const success = await auth.login(identifier.value, password.value);

    if (success) {
      auth.triggerGreetingAndNavigate();
    } else {
      error.value = 'Username/Email atau password salah.';
    }
  } catch (err) {
    error.value = err.message || 'Terjadi kesalahan saat login. Coba lagi.';
  } finally {
    loading.value = false;
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

/* ===== ROOT WARNA ===== */
:root {
  --neon-blue: #4da3ff;
  --dark-blue: #1e90ff;
  --deep-blue: #0a1f44;
  --bg-gradient: linear-gradient(135deg, #0a1f44, #0f3c91, #1e90ff);
  --card-bg: linear-gradient(160deg, #0b1d3a, #122b55);
  --input-bg: #0b1d3a;
}

/* ===== WRAPPER ===== */
.login-content-wrapper {
  background: var(--bg-gradient);
  color: #eaf2ff;
  font-family: 'Press Start 2P', monospace;
  min-height: calc(100vh - var(--navbar-height, 0px) - var(--footer-height, 0px));
  display: flex;
  flex-direction: column;
  flex: 1;
}

/* ===== MAIN ===== */
.main-content {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem 1rem;
}

/* ===== CARD ===== */
.form-card {
  background: var(--card-bg);
  border: 2px solid var(--neon-blue);
  padding: 2.5rem;
  border-radius: 12px;
  text-align: center;
  width: 95%;
  max-width: 1000px;
  box-shadow:
    0 0 20px rgba(77,163,255,0.7),
    0 0 35px rgba(77,163,255,0.4);
  transition: transform 0.3s ease-in-out;
}

.form-card:hover {
  transform: translateY(-5px);
}

/* ===== TITLE ===== */
.form-card h1 {
  font-size: 1.5rem;
  margin-bottom: 1.2rem;
  color: var(--neon-blue);
  text-shadow: 0 0 8px rgba(77,163,255,0.8);
}

.form-card .tagline {
  font-size: 0.9rem;
  margin-bottom: 2rem;
  color: #cbdcff;
}

/* ===== FORM ===== */
.login-form {
  display: flex;
  flex-direction: column;
  gap: 1.2rem;
}

.form-group {
  text-align: left;
}

.form-group label {
  margin-bottom: 0.6rem;
  font-size: 0.9rem;
  color: #ffffff;
}

/* ===== INPUT ===== */
.form-group input {
  width: 100%;
  padding: 0.85rem 10px;
  border: 2px solid var(--neon-blue);
  background-color: var(--input-bg);
  color: #ffffff;
  font-family: 'Press Start 2P', monospace;
  border-radius: 6px;
  font-size: 0.9rem;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: var(--dark-blue);
  box-shadow: 0 0 12px rgba(77,163,255,0.8);
}

.form-group input::placeholder {
  color: #9fbfff;
  opacity: 0.7;
}

/* ===== BUTTON ===== */
.submit-button {
  background: linear-gradient(135deg, var(--neon-blue), var(--dark-blue));
  color: var(--deep-blue);
  border: none;
  padding: 1.2rem;
  font-family: 'Press Start 2P', monospace;
  cursor: pointer;
  border-radius: 8px;
  font-size: 1.1rem;
  transition: all 0.3s ease;
  margin-top: 1.5rem;
  box-shadow:
    0 0 15px var(--neon-blue),
    0 0 30px var(--neon-blue);
}

.submit-button:hover:not(:disabled) {
  color: #ffffff;
  transform: translateY(-3px);
  box-shadow:
    0 0 20px var(--neon-blue),
    0 0 40px var(--neon-blue);
}

.submit-button:disabled {
  background: #555;
  color: #aaa;
  cursor: not-allowed;
  opacity: 0.6;
  box-shadow: none;
  transform: none;
}

/* ===== ERROR ===== */
.error-message {
  margin-top: 1rem;
  font-size: 0.85rem;
  color: #ff4d4d;
  background-color: rgba(255, 77, 77, 0.15);
  border: 1px solid #ff4d4d;
  padding: 0.75rem;
  border-radius: 6px;
}

/* ===== LINKS ===== */
.forgot-password-link,
.register-link {
  margin-top: 1rem;
  font-size: 0.8rem;
  color: #cbdcff;
}

.register-link {
  margin-top: 2rem;
}

.forgot-password-link a,
.register-link a {
  color: var(--neon-blue);
  text-decoration: none;
  transition: color 0.3s, text-decoration 0.3s;
}

.forgot-password-link a:hover,
.register-link a:hover {
  color: var(--dark-blue);
  text-decoration: underline;
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  .form-card {
    padding: 2rem;
  }
  .form-card h1 {
    font-size: 1.3rem;
  }
}

@media (max-width: 480px) {
  .form-card {
    padding: 1.5rem;
  }
  .form-card h1 {
    font-size: 1.1rem;
  }
}
</style>
