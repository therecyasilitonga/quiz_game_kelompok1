<template>
  <div class="info-content-wrapper">
    <main class="main-content">

      <!-- ABOUT -->
      <section class="section">
        <h1>About FUN VOCABULARY LEARNING QUIZ</h1>
        <p>
          FUN VOCABULARY LEARNING QUIZ adalah game edukasi interaktif
          yang membantu pemain meningkatkan kosakata bahasa Inggris
          melalui kuis yang seru, cepat, dan menantang.
          Belajar vocabulary jadi lebih menyenangkan dan tidak membosankan.
        </p>
      </section>

      <!-- HOW TO PLAY -->
      <section class="section">
        <h2>🎮 How to Play</h2>
        <ul>
          <li>Baca setiap pertanyaan dengan teliti.</li>
          <li>Pilih jawaban yang paling tepat.</li>
          <li>Setiap soal menguji pemahaman kosakata.</li>
          <li>Hasil dan skor ditampilkan di akhir permainan.</li>
        </ul>
      </section>

      <!-- WHAT THE GAME MEASURES -->
      <section class="section">
        <h2>🧠 What This Game Measures</h2>
        <p>
          Game ini mengukur kemampuan vocabulary pemain berdasarkan:
        </p>
        <ul>
          <li>Pemahaman arti kata bahasa Inggris.</li>
          <li>Ketepatan dalam memilih kata.</li>
          <li>Kecepatan menjawab soal.</li>
          <li>Daya ingat kosakata.</li>
          <li>Konsistensi pemahaman bahasa.</li>
        </ul>
      </section>

      <!-- DEVELOPER -->
      <section class="section">
        <h2>👨‍💻 Developer</h2>
        <p>
          Game ini dikembangkan oleh <strong>Kelompok 1</strong>
          sebagai project game edukasi berbasis kuis interaktif
          untuk pembelajaran kosakata bahasa Inggris.
        </p>
      </section>

    </main>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600&display=swap');

.info-content-wrapper {
  background: linear-gradient(135deg, #0a1e3f, #020c1b);
  color: #d6f1ff;
  font-family: 'Orbitron', sans-serif;
  min-height: 100vh;
  display: flex;
  justify-content: center;
}

.main-content {
  width: 100%;
  max-width: 900px;
  padding: 2.5rem;
}

.section {
  background: rgba(30, 80, 150, 0.35);
  border: 1px solid rgba(120, 200, 255, 0.35);
  border-radius: 16px;
  padding: 1.8rem;
  margin-bottom: 2rem;
  backdrop-filter: blur(8px);
  box-shadow: 0 0 25px rgba(100, 200, 255, 0.25);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.section:hover {
  transform: translateY(-4px);
  box-shadow: 0 0 35px rgba(140, 220, 255, 0.45);
}

.section h1,
.section h2 {
  color: #7dd3fc;
  font-size: 1.2rem;
  margin-bottom: 1rem;
  text-shadow: 0 0 10px rgba(125, 211, 252, 0.8);
}

.section p {
  font-size: 0.85rem;
  line-height: 1.8;
  text-align: justify;
}

.section ul {
  padding-left: 1.2rem;
}

.section ul li {
  margin-bottom: 0.6rem;
  font-size: 0.85rem;
  line-height: 1.6;
}

/* Responsive */
@media (max-width: 768px) {
  .main-content {
    padding: 1.8rem;
  }
  .section h1,
  .section h2 {
    font-size: 1.05rem;
  }
  .section p,
  .section ul li {
    font-size: 0.8rem;
  }
}

@media (max-width: 480px) {
  .main-content {
    padding: 1.2rem;
  }
  .section h1,
  .section h2 {
    font-size: 0.95rem;
  }
  .section p,
  .section ul li {
    font-size: 0.75rem;
  }
}
</style>
