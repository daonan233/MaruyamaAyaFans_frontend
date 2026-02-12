<template>
  <section id="interactive" class="interactive-section">
    <div class="section-header">
      <h2 class="section-title">
        <span class="title-decoration">✨</span>
        互动彩蛋
        <span class="title-decoration">✨</span>
      </h2>
    </div>

    <div class="interactive-grid">
      <div class="interactive-card" @click="triggerConfetti">
        <div class="card-bg"></div>
        <div class="card-content">
          <div class="card-emoji">🎉</div>
          <h3 style="user-select: none">庆祝时刻</h3>
          <p style="user-select: none">点击释放彩带！</p>
        </div>
      </div>

      <div class="interactive-card" @click="showRandomQuote">
        <div class="card-bg"></div>
        <div class="card-content">
          <div class="card-emoji">💭</div>
          <h3 style="user-select: none">随机语录</h3>
          <p style="user-select: none">这就是怕死怕累，听彩彩说</p>
        </div>
      </div>

      <div class="interactive-card" @click="generateColor">
        <div class="card-bg"></div>
        <div class="card-content">
          <div class="card-emoji">🎨</div>
          <h3 style="user-select: none">专属色彩</h3>
          <p style="user-select: none">生成你的粉色系</p>
        </div>
      </div>

      <!--      <div class="interactive-card" @click="heartRain">-->
      <!--        <div class="card-bg"></div>-->
      <!--        <div class="card-content">-->
      <!--          <div class="card-emoji">💖</div>-->
      <!--          <h3>爱心雨</h3>-->
      <!--          <p>撒下爱的祝福</p>-->
      <!--        </div>-->
      <!--      </div>-->
    </div>

    <div ref="confettiContainer" class="confetti-container"></div>
  </section>
</template>

<script>
import {ElMessage, ElNotification} from 'element-plus';

export default {
  name: 'InteractiveSection',
  data() {
    return {
      quotes: [
        '丸山添彩！',
        '只要不放弃，就一定能创造奇迹！',
        '……哎？还有时间吗！？呃、呃呃……我该说什么？',
        '希望我们的心意，能传达到那个座位',
        '就算是我，也绝不会输！'
      ]
    };
  },
  methods: {
    triggerConfetti() {
      const container = this.$refs.confettiContainer;
      if (!container) return;

      for (let i = 0; i < 50; i++) {
        const confetti = document.createElement('div');
        confetti.className = 'confetti-piece';
        confetti.style.left = `${Math.random() * 100}%`;
        confetti.style.backgroundColor = ['#FFB6D9', '#FFD4E5', '#FFC0E0', '#FFAAD5', '#FF94CA'][Math.floor(Math.random() * 5)];
        confetti.style.animationDelay = `${Math.random() * 0.5}s`;
        container.appendChild(confetti);

        setTimeout(() => confetti.remove(), 3000);
      }

      ElMessage.success('🎉 庆祝时刻！');
    },
    showRandomQuote() {
      const randomQuote = this.quotes[Math.floor(Math.random() * this.quotes.length)];
      ElNotification({
        title: '彩的话',
        message: randomQuote,
        type: 'success',
        duration: 3000,
        position: 'top-right'
      });
    },
    generateColor() {
      const hue = Math.floor(Math.random() * 30) + 320;
      const saturation = Math.floor(Math.random() * 20) + 80;
      const lightness = Math.floor(Math.random() * 20) + 75;
      const color = `hsl(${hue}, ${saturation}%, ${lightness}%)`;

      ElNotification({
        title: '你的专属粉色',
        message: `<div style="display: flex; align-items: center; gap: 1vw;">
          <div style="width: 5vw; height: 5vw; background: ${color}; border-radius: 50%; border: 0.2vw solid #fff; box-shadow: 0 0 1vw rgba(0,0,0,0.2);"></div>
          <span style="font-family: 'Quicksand', sans-serif; color: #333;">${color}</span>
        </div>`,
        dangerouslyUseHTMLString: true,
        duration: 5000,
        position: 'bottom-right'
      });
    },
    heartRain() {
      const container = this.$refs.confettiContainer;
      if (!container) return;

      for (let i = 0; i < 30; i++) {
        const heart = document.createElement('div');
        heart.className = 'heart-piece';
        heart.textContent = '💖';
        heart.style.left = `${Math.random() * 100}%`;
        heart.style.fontSize = `${Math.random() * 2 + 1}vw`;
        heart.style.animationDelay = `${Math.random() * 0.5}s`;
        heart.style.animationDuration = `${Math.random() * 2 + 2}s`;
        container.appendChild(heart);

        setTimeout(() => heart.remove(), 4000);
      }

      ElMessage.success('💖 送你满满的爱！');
    }
  }
};
</script>

<style scoped>
.interactive-section {
  background: linear-gradient(135deg, rgba(255, 240, 245, 0.8) 0%, rgba(255, 212, 229, 0.8) 100%);
  border-radius: 3vw;
  margin: 5vh 3vw;
  position: relative;
  overflow: hidden;
  padding: 8vh 5vw;
  min-height: 60vh;
}

.section-header {
  text-align: center;
  margin-bottom: 6vh;
}

.section-title {
  font-family: 'Pacifico', 'Noto Sans JP', cursive;
  font-size: 3.5vw;
  color: #FF6FB5;
  display: inline-flex;
  align-items: center;
  gap: 1.5vw;
  position: relative;
}

.section-title::before,
.section-title::after {
  content: '';
  width: 5vw;
  height: 0.3vw;
  background: linear-gradient(90deg, transparent 0%, #FFB6D9 50%, transparent 100%);
}

.title-decoration {
  font-size: 2vw;
  animation: rotate-decoration 3s linear infinite;
}

@keyframes rotate-decoration {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.interactive-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(18vw, 1fr));
  gap: 3vw;
  max-width: 80vw;
  margin: 0 auto;
}

.interactive-card {
  aspect-ratio: 1;
  border-radius: 2vw;
  position: relative;
  overflow: hidden;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.interactive-card:hover {
  transform: translateY(-1vh) scale(1.05);
}

.card-bg {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%);
  opacity: 0.9;
  transition: opacity 0.3s ease;
}

.interactive-card:hover .card-bg {
  opacity: 1;
}

.card-content {
  position: relative;
  z-index: 1;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 3vh 2vw;
  color: white;
  text-align: center;
}

.card-emoji {
  font-size: 4vw;
  margin-bottom: 2vh;
  animation: bounce 2s ease-in-out infinite;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-1vh);
  }
}

.card-content h3 {
  font-size: 1.5vw;
  margin-bottom: 1vh;
  font-weight: 700;
}

.card-content p {
  font-size: 1vw;
  opacity: 0.9;
}

.confetti-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none;
  z-index: 9999;
}

.confetti-piece {
  position: absolute;
  width: 1vw;
  height: 1vw;
  top: -5vh;
  animation: confetti-fall 3s linear forwards;
}

@keyframes confetti-fall {
  to {
    transform: translateY(110vh) rotate(720deg);
    opacity: 0;
  }
}

.heart-piece {
  position: absolute;
  top: -5vh;
  animation: heart-fall 4s linear forwards;
}

@keyframes heart-fall {
  to {
    transform: translateY(110vh) rotate(360deg);
    opacity: 0;
  }
}
</style>
