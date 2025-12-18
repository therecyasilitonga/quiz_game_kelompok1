<template>
  <div class="karakter-content-wrapper">
    <main class="main-content">
      <div class="karakter-card">

        <h1>📘 English Basic Learning</h1>
        <p class="subtitle">Belajar Bahasa Inggris dari dasar + game sederhana</p>

        <!-- ===== MENU ===== -->
        <div v-if="page === 'menu'" class="menu">
  <button @click="openMaterial('vocab-basic')">📚 Vocabulary</button>
  <button @click="openMaterial('noun')">📦 Noun</button>
  <button @click="openMaterial('verb')">🔤 Verb</button>
  <button @click="openMaterial('adjective')">🎨 Adjective</button>
  <button @click="openMaterial('pronoun')">👤 Pronoun</button>
  <button @click="openMaterial('function')">🗣️ Function</button>
  <button @click="openMaterial('sentence')">📝 Sentence</button>
  <button @click="openMaterial('tenses')">⏱️ Tenses</button>
  <button @click="openMaterial('expression')">💬 Expression</button>
  <button @click="startGame">🎮 Game Quiz</button>
</div>


        <!-- ===== MATERIAL ===== -->
        <div v-if="page === 'material'" class="material">
          <button class="back-btn" @click="page='menu'">← Back</button>

          <h2>{{ material.title }}</h2>
          <p class="desc">{{ material.desc }}</p>

          <ul>
            <li v-for="(item,i) in material.examples" :key="i">
              {{ item }}
            </li>
          </ul>
        </div>

        <!-- ===== GAME ===== -->
        <div v-if="page === 'game'" class="game">
          <button class="back-btn" @click="page='menu'">← Back</button>

          <div class="progress">
            Question {{ index + 1 }} / {{ questions.length }}
          </div>

          <h3>{{ questions[index].q }}</h3>

          <div class="choices">
            <button
              v-for="(c,i) in questions[index].choices"
              :key="i"
              @click="answer(c)"
            >
              {{ c }}
            </button>
          </div>

          <p v-if="feedback" class="feedback">{{ feedback }}</p>
        </div>

        <!-- ===== RESULT ===== -->
        <div v-if="page === 'result'" class="result">
          <h2>🎉 Result</h2>
          <p>Your Score: <strong>{{ score }}</strong></p>

          <button @click="restartGame">Play Again</button>
          <button @click="page='menu'">Back to Menu</button>
        </div>

      </div>
    </main>
  </div>
</template>


<script setup>
import { ref } from "vue";


const timeLeft = ref(20);
let timer = null;

/* ===== STATE ===== */
const page = ref("menu");
const index = ref(0);
const score = ref(0);
const feedback = ref("");
/* ===== MATERIAL ===== */
const material = ref({ title: "", desc: "", examples: [] });

function openMaterial(type) {
  page.value = "material";

  /* ================= BASIC VOCAB ================= */
  if (type === "vocab-basic") {
    material.value = {
      title: "Basic Vocabulary (Kosakata Dasar)",
      desc: "Kosakata dasar yang sering digunakan dalam kehidupan sehari-hari.",
      examples: [
        "I = Saya",
        "You = Kamu",
        "He = Dia (laki-laki)",
        "She = Dia (perempuan)",
        "It = Itu",
        "We = Kami",
        "They = Mereka",
        "Yes = Ya",
        "No = Tidak",
        "Please = Tolong",
        "Sorry = Maaf",
        "Thank you = Terima kasih",
        "Book = Buku",
        "Pen = Pulpen",
        "School = Sekolah"
      ]
    };
  }

  /* ================= NOUN ================= */
  if (type === "noun") {
    material.value = {
      title: "Noun (Kata Benda)",
      desc: "Noun adalah kata yang digunakan untuk menamai orang, tempat, benda, atau hewan.",
      examples: [
        "Teacher = Guru",
        "Student = Murid",
        "School = Sekolah",
        "Classroom = Ruang kelas",
        "Table = Meja",
        "Chair = Kursi",
        "Bag = Tas",
        "Book = Buku",
        "Dog = Anjing",
        "Cat = Kucing",
        "City = Kota",
        "House = Rumah"
      ]
    };
  }

  /* ================= VERB ================= */
  if (type === "verb") {
    material.value = {
      title: "Verb (Kata Kerja)",
      desc: "Verb adalah kata kerja yang menunjukkan aktivitas atau keadaan.",
      examples: [
        "eat = makan → I eat rice",
        "drink = minum → She drinks water",
        "go = pergi → They go to school",
        "come = datang → Come here",
        "study = belajar → We study English",
        "read = membaca → He reads a book",
        "write = menulis → She writes a letter",
        "play = bermain → They play football",
        "sleep = tidur → I sleep early",
        "watch = menonton → I watch TV"
      ]
    };
  }

  /* ================= ADJECTIVE ================= */
  if (type === "adjective") {
    material.value = {
      title: "Adjective (Kata Sifat)",
      desc: "Adjective adalah kata yang digunakan untuk menjelaskan sifat atau keadaan noun.",
      examples: [
        "Big = Besar",
        "Small = Kecil",
        "Good = Baik",
        "Bad = Buruk",
        "Happy = Senang",
        "Sad = Sedih",
        "Fast = Cepat",
        "Slow = Lambat",
        "Beautiful = Cantik",
        "Smart = Pintar"
      ]
    };
  }

  /* ================= PRONOUN ================= */
  if (type === "pronoun") {
    material.value = {
      title: "Pronoun (Kata Ganti)",
      desc: "Pronoun digunakan untuk menggantikan noun agar tidak terjadi pengulangan.",
      examples: [
        "I = Saya",
        "You = Kamu",
        "He = Dia (laki-laki)",
        "She = Dia (perempuan)",
        "It = Itu",
        "We = Kami",
        "They = Mereka",
        "My = Milik saya",
        "Your = Milik kamu",
        "His = Milik dia (laki-laki)"
      ]
    };
  }

  /* ================= FUNCTION ================= */
  if (type === "function") {
    material.value = {
      title: "Function (Fungsi Kalimat)",
      desc: "Function adalah tujuan penggunaan kalimat dalam percakapan.",
      examples: [
        "Greeting → Hello!",
        "Introducing → My name is Rina",
        "Asking → What is your name?",
        "Thanking → Thank you very much",
        "Apologizing → I'm sorry",
        "Request → Please help me",
        "Offering → Can I help you?",
        "Asking permission → May I come in?",
        "Giving opinion → I think it is good",
        "Leave-taking → Goodbye"
      ]
    };
  }

  /* ================= SENTENCE ================= */
  if (type === "sentence") {
    material.value = {
      title: "Simple Sentence (Kalimat Sederhana)",
      desc: "Kalimat sederhana biasanya terdiri dari Subject + Verb + Object.",
      examples: [
        "I eat rice",
        "She reads a book",
        "They play football",
        "We study English",
        "He drinks milk",
        "The teacher teaches students",
        "I go to school every day"
      ]
    };
  }

  /* ================= TENSES ================= */
  if (type === "tenses") {
    material.value = {
      title: "Basic Tenses (Waktu Dasar)",
      desc: "Tenses digunakan untuk menunjukkan waktu terjadinya suatu kejadian.",
      examples: [
        "Simple Present → I eat rice",
        "Simple Present → She studies English",
        "Present Continuous → I am studying now",
        "Simple Past → I ate rice yesterday",
        "Simple Past → She went to school",
        "Simple Future → I will study tonight",
        "Present Perfect → I have finished my homework"
      ]
    };
  }

  /* ================= DAILY EXPRESSION ================= */
  if (type === "expression") {
    material.value = {
      title: "Daily Expressions (Ungkapan Sehari-hari)",
      desc: "Ungkapan yang sering digunakan dalam percakapan sehari-hari.",
      examples: [
        "How are you?",
        "I'm fine, thank you",
        "Nice to meet you",
        "Excuse me",
        "See you later",
        "Take care",
        "Good luck",
        "Have a nice day"
      ]
    };
  }
}


/* ===== GAME DATA ===== */
const questions = [
  // ===== VOCABULARY =====
  { q: "What is the meaning of 'Book'?", answer: "Buku", choices: ["Pulpen", "Buku", "Tas"] },
  { q: "What is the English word for 'Sekolah'?", answer: "School", choices: ["House", "School", "Teacher"] },

  // ===== NOUN =====
  { q: "Which one is a noun?", answer: "Teacher", choices: ["Teacher", "Run", "Fast"] },
  { q: "What is the meaning of 'Chair'?", answer: "Kursi", choices: ["Meja", "Kursi", "Pintu"] },

  // ===== VERB =====
  { q: "Which one is a verb?", answer: "Eat", choices: ["Book", "Eat", "School"] },
  { q: "What does 'go' mean?", answer: "Pergi", choices: ["Makan", "Pergi", "Tidur"] },

  // ===== ADJECTIVE =====
  { q: "Which word describes something?", answer: "Big", choices: ["Run", "Big", "Teacher"] },
  { q: "What is the opposite of 'Fast'?", answer: "Slow", choices: ["Slow", "Good", "Tall"] },

  // ===== PRONOUN =====
  { q: "Which one is a pronoun?", answer: "She", choices: ["She", "Book", "Run"] },
  { q: "‘They’ is used for…", answer: "Many people or things", choices: ["One person", "Many people or things", "One object"] },

  // ===== FUNCTION =====
  { q: "Which sentence is used for greeting?", answer: "Hello!", choices: ["Hello!", "Thank you", "Goodbye"] },
  { q: "Which sentence is used to say thank you?", answer: "Thank you very much", choices: ["I'm sorry", "Thank you very much", "Excuse me"] },

  // ===== SIMPLE SENTENCE =====
  { q: "Which one is a correct sentence?", answer: "I eat rice", choices: ["Eat rice I", "I eat rice", "Rice eat I"] },
  { q: "Which sentence is correct?", answer: "She reads a book", choices: ["She read a book", "She reads a book", "She reading book"] },

  // ===== TENSES =====
  { q: "Which sentence is Simple Present?", answer: "I study English", choices: ["I studied English", "I study English", "I am studying English"] },
  { q: "Which sentence is Simple Past?", answer: "I ate rice yesterday", choices: ["I eat rice", "I am eating rice", "I ate rice yesterday"] },

  // ===== DAILY EXPRESSION =====
  { q: "What do you say when meeting someone?", answer: "Nice to meet you", choices: ["Good night", "Nice to meet you", "See you later"] },
  { q: "Which expression asks permission?", answer: "May I come in?", choices: ["Thank you", "May I come in?", "Goodbye"] },

  // ===== MIX =====
  { q: "Which one is an adjective?", answer: "Beautiful", choices: ["Beautiful", "Run", "Teacher"] },
  { q: "Which one is a verb?", answer: "Play", choices: ["Play", "Book", "Chair"] }
];



function startTimer() {
  timeLeft.value = 20;

  timer = setInterval(() => {
    timeLeft.value--;

    if (timeLeft.value <= 0) {
      clearInterval(timer);
      feedback.value = "⏰ Time's up!";

      setTimeout(nextQuestion, 700);
    }
  }, 1000);
}

function nextQuestion() {
  feedback.value = "";

  if (index.value < questions.length - 1) {
    index.value++;
    startTimer();
  } else {
    page.value = "result";
  }
}

function startGame() {
  page.value = "game";
  index.value = 0;
  score.value = 0;
  feedback.value = "";
  startTimer();
}


/* ===== GAME LOGIC ===== */
function answer(choice) {
  clearInterval(timer);

  if (choice === questions[index.value].answer) {
    score.value += 5;
    feedback.value = "✅ Correct!";
  } else {
    feedback.value = "❌ Wrong!";
  }

  setTimeout(() => {
    nextQuestion();
  }, 700);
}


function restartGame() {
  clearInterval(timer);
  startGame();
}

</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

/* ===== ROOT ===== */
.karakter-content-wrapper {
  background: linear-gradient(135deg, #0b1220, #020617);
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 40px 20px;
  font-family: 'Press Start 2P', monospace;
  color: #e0f2ff;
}

.main-content {
  width: 100%;
  max-width: 880px;
}

/* ===== CARD ===== */
.karakter-card {
  background: linear-gradient(180deg, #0f172a, #020617);
  border-radius: 18px;
  padding: 32px;
  box-shadow:
    0 20px 40px rgba(0,0,0,0.4),
    inset 0 0 0 1px rgba(56,189,248,0.35);
}

/* ===== HEADER ===== */
h1 {
  font-size: 1.2rem;
  color: #7dd3fc;
  margin-bottom: 10px;
  text-align: center;
  text-shadow: 0 0 8px rgba(56,189,248,.5);
}

.subtitle {
  font-size: .65rem;
  color: #bae6fd;
  margin-bottom: 26px;
  text-align: center;
  line-height: 1.7;
}

/* ===== MENU ===== */
.menu {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px,1fr));
  gap: 18px;
}

.menu button {
  background: linear-gradient(180deg,#020617,#0f172a);
  border-radius: 16px;
  padding: 18px;
  border: 1px solid rgba(56,189,248,0.45);
  color: #e0f2ff;
  cursor: pointer;
  transition: all .25s ease;
  box-shadow: inset 0 0 0 1px rgba(255,255,255,0.04);
}

.menu button:hover {
  transform: translateY(-6px);
  box-shadow: 0 12px 30px rgba(56,189,248,0.45);
}

/* ===== MATERIAL ===== */
.material {
  text-align: left;
}

.material h2 {
  font-size: .9rem;
  color: #7dd3fc;
  margin-bottom: 14px;
}

.material .desc {
  font-size: .65rem;
  color: #bae6fd;
  margin-bottom: 18px;
  line-height: 1.7;
}

.material ul {
  padding-left: 18px;
}

.material li {
  font-size: .65rem;
  margin-bottom: 10px;
  color: #e0f2ff;
}

/* ===== GAME ===== */
.game {
  text-align: center;
}

.progress {
  font-size: .6rem;
  color: #bae6fd;
  margin-bottom: 12px;
}

.game h3 {
  font-size: .75rem;
  margin-bottom: 18px;
  color: #e0f2ff;
}

.choices {
  display: grid;
  gap: 14px;
}

.choices button {
  background: linear-gradient(180deg,#020617,#0f172a);
  border-radius: 14px;
  padding: 14px;
  border: 1px solid rgba(56,189,248,.5);
  color: #e0f2ff;
  cursor: pointer;
  transition: all .2s ease;
}

.choices button:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 24px rgba(56,189,248,.45);
}

.feedback {
  margin-top: 14px;
  font-size: .65rem;
  color: #7dd3fc;
}

/* ===== RESULT ===== */
.result {
  text-align: center;
}

.result h2 {
  font-size: 1rem;
  color: #7dd3fc;
  margin-bottom: 14px;
}

.result p {
  font-size: .65rem;
  margin-bottom: 18px;
  color: #e0f2ff;
}

.result button {
  background: linear-gradient(180deg,#38bdf8,#0ea5e9);
  border-radius: 14px;
  padding: 12px 16px;
  border: none;
  color: #020617;
  font-size: .65rem;
  margin: 6px;
  cursor: pointer;
}

.result button:last-child {
  background: transparent;
  border: 1px solid #38bdf8;
  color: #e0f2ff;
}

/* ===== BACK BUTTON ===== */
.back-btn {
  margin-bottom: 14px;
  background: transparent;
  border: 1px solid #38bdf8;
  padding: 6px 12px;
  border-radius: 10px;
  font-size: .6rem;
  color: #7dd3fc;
  cursor: pointer;
}

/* ===== RESPONSIVE ===== */
@media (max-width: 720px) {
  .karakter-card {
    padding: 22px;
  }
  h1 {
    font-size: 1rem;
  }
}
</style>
