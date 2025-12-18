<template>
  <div class="register-container">
    <main class="main-content">
      <div class="form-card">
        <h1>Selamat datang di laman Register FUN VOCABULARY LEARNING QUIZ Game</h1>
        <p class="tagline">ayo bermain sambil belajar!</p>
        <form @submit.prevent="handleRegister" class="register-form">
          <div class="form-group">
            <label for="regUsername">Username</label>
            <input
              type="text"
              id="regUsername"
              v-model="username"
              placeholder="Buat Username Anda"
              required
            />
          </div>
          <div class="form-group">
            <label for="regEmail">Email</label>
            <input
              type="email"
              id="regEmail"
              v-model="email"
              placeholder="Masukkan Email Anda"
              required
            />
          </div>
          <div class="form-group">
            <label for="regPassword">Password</label>
            <input
              type="password"
              id="regPassword"
              v-model="password"
              placeholder="Buat Password Anda"
              required
            />
          </div>
          <div class="form-group">
            <label for="confirmPassword">Konfirmasi Password</label>
            <input
              type="password"
              id="confirmPassword"
              v-model="confirmPassword"
              placeholder="Konfirmasi Password Anda"
              required
            />
          </div>
          <button type="submit" class="submit-button" :disabled="loading">
            {{ loading ? 'Mendaftar...' : 'Daftar' }}
          </button>
          <p v-if="error" class="error-message">{{ error }}</p>
          <p v-if="success" class="success-message">{{ success }}</p>
        </form>
        <p class="login-link">
          Sudah punya akun?
          <router-link to="/login">Login sekarang</router-link>
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

const username = ref('');
const email = ref('');
const password = ref('');
const confirmPassword = ref('');
const loading = ref(false);
const error = ref(null);
const success = ref(null);

const handleRegister = async () => {
  loading.value = true;
  error.value = null;
  success.value = null;

  if (password.value !== confirmPassword.value) {
    error.value = 'Password dan konfirmasi password tidak cocok!';
    loading.value = false;
    return;
  }

  if (username.value.length < 3) {
    error.value = 'Username minimal 3 karakter.';
    loading.value = false;
    return;
  }

  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email.value)) {
    error.value = 'Format email tidak valid.';
    loading.value = false;
    return;
  }

  if (password.value.length < 6) {
    error.value = 'Password minimal 6 karakter.';
    loading.value = false;
    return;
  }

  try {
    const existingUsers = JSON.parse(localStorage.getItem('registeredUsers') || '[]');
    const isExisting = existingUsers.some(
      user => user.username === username.value || user.email === email.value
    );

    if (isExisting) {
      throw new Error('Username atau Email sudah terdaftar.');
    }

    await new Promise(resolve => setTimeout(resolve, 1500));

    const newUser = {
      id: Date.now(),
      username: username.value,
      email: email.value,
      password: password.value
    };

    existingUsers.push(newUser);
    localStorage.setItem('registeredUsers', JSON.stringify(existingUsers));

    success.value = 'Pendaftaran berhasil! Mengarahkan Anda ke halaman utama...';

    const loginSuccess = await auth.login(newUser.email, newUser.password);

    if (loginSuccess) {
      auth.triggerGreetingAndNavigate();
    } else {
      setTimeout(() => {
        router.push('/login');
      }, 1000);
    }
  } catch (err) {
    console.error('Registration error:', err);
    error.value = err.message || 'Terjadi kesalahan saat mendaftar. Coba lagi.';
  } finally {
    loading.value = false;
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

/* ===== BACKGROUND UTAMA (BIRU GRADASI) ===== */
.register-container {
  background: linear-gradient(135deg, #0a1f44, #0f3c91, #1e90ff);
  color: #ffffff;
  font-family: 'Press Start 2P', monospace;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* ===== KONTEN ===== */
.main-content {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem 1rem;
}

/* ===== CARD FORM ===== */
.form-card {
  background: linear-gradient(160deg, #0b1d3a, #122b55);
  border: 2px solid #4da3ff;
  padding: 2.5rem 2rem;
  border-radius: 12px;
  text-align: center;
  width: 95%;
  max-width: 1100px;
  box-shadow: 0 0 25px rgba(77, 163, 255, 0.6);
  transition: transform 0.3s ease-in-out;
}

.form-card:hover {
  transform: translateY(-5px);
}

/* ===== JUDUL ===== */
.form-card h1 {
  font-size: 1.5rem;
  margin-bottom: 1.2rem;
  color: #4da3ff;
  text-shadow: 0 0 6px rgba(77, 163, 255, 0.8);
}

.form-card .tagline {
  font-size: 0.9rem;
  margin-bottom: 2rem;
  color: #cbdcff;
}

/* ===== FORM ===== */
.register-form {
  display: flex;
  flex-direction: column;
  gap: 1.2rem;
}

.form-group {
  text-align: left;
}

.form-group label {
  display: block;
  margin-bottom: 0.6rem;
  font-size: 0.9rem;
  color: #ffffff;
}

/* ===== INPUT ===== */
.form-group input {
  width: 100%;
  padding: 0.85rem 10px;
  border: 2px solid #4da3ff;
  background-color: #0b1d3a;
  color: #ffffff;
  font-family: 'Press Start 2P', monospace;
  border-radius: 6px;
  font-size: 0.9rem;
  transition: border-color 0.3s, box-shadow 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: #1e90ff;
  box-shadow: 0 0 12px rgba(30, 144, 255, 0.8);
}

.form-group input::placeholder {
  color: #b0cfff;
  opacity: 0.7;
}

/* ===== BUTTON ===== */
.submit-button {
  background: linear-gradient(135deg, #4da3ff, #1e90ff);
  color: #0a1f44;
  border: none;
  padding: 1.2rem;
  font-family: 'Press Start 2P', monospace;
  cursor: pointer;
  border-radius: 8px;
  font-size: 1.1rem;
  transition: all 0.3s ease;
  margin-top: 1.5rem;
  box-shadow: 0 6px 18px rgba(77, 163, 255, 0.6);
}

.submit-button:hover:not(:disabled) {
  background: linear-gradient(135deg, #1e90ff, #007bff);
  color: #ffffff;
  transform: translateY(-3px);
  box-shadow: 0 10px 25px rgba(30, 144, 255, 0.8);
}

.submit-button:disabled {
  background-color: #555;
  color: #aaa;
  cursor: not-allowed;
  opacity: 0.6;
}

/* ===== MESSAGE ===== */
.error-message {
  margin-top: 1rem;
  font-size: 0.85rem;
  color: #ff4d4d;
  background-color: rgba(255, 77, 77, 0.15);
  border: 1px solid #ff4d4d;
  padding: 0.75rem;
  border-radius: 6px;
}

.success-message {
  margin-top: 1rem;
  font-size: 0.85rem;
  color: #4da3ff;
  background-color: rgba(77, 163, 255, 0.15);
  border: 1px solid #4da3ff;
  padding: 0.75rem;
  border-radius: 6px;
}

/* ===== LINK LOGIN ===== */
.login-link {
  margin-top: 2rem;
  font-size: 0.85rem;
  color: #cbdcff;
}

.login-link a {
  color: #4da3ff;
  text-decoration: none;
  transition: color 0.3s, text-decoration 0.3s;
}

.login-link a:hover {
  color: #1e90ff;
  text-decoration: underline;
}

/* ===== RESPONSIVE ===== */
@media (max-width: 768px) {
  .form-card {
    width: 95%;
  }
}

@media (max-width: 480px) {
  .form-card {
    width: 95%;
  }
}
</style>
