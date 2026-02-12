<template>
  <section id="hero" class="hero-section">
    <div class="hero-background">
      <div class="gradient-orb orb-1"></div>
      <div class="gradient-orb orb-2"></div>
      <div class="gradient-orb orb-3"></div>
    </div>

    <el-carousel
        :interval="4000"
        height="75vh"
        type="card"
        @change="handleCarouselChange"
    >
      <el-carousel-item v-for="(item, index) in carouselItems" :key="index">
        <div :style="{ backgroundImage: `url(${item.image})` }" class="carousel-card">
          <div class="carousel-overlay"></div>
          <div class="carousel-content">
            <h2 class="carousel-title">{{ item.title }}</h2>
            <p class="carousel-desc">{{ item.desc }}</p>
            <div class="sparkles">
              <div v-for="n in 5" :key="n" class="sparkle"></div>
            </div>
          </div>
        </div>
      </el-carousel-item>
    </el-carousel>

    <div class="hero-title">
      <h1 class="main-title">
        <span v-for="(char, i) in titleChars" :key="i" :style="{ animationDelay: `${i * 0.1}s` }" class="title-char">
          {{ char }}
        </span>
      </h1>
      <p class="subtitle">{{ currentQuote }}</p>
    </div>
  </section>
</template>

<script>
export default {
  name: 'HeroSection',
  data() {
    return {
      carouselItems: [
        {
          image: 'https://via.placeholder.com/800x600/FFB6D9/FFFFFF?text=Aya+Stage+1',
          title: '舞台之星',
          desc: '闪耀的Pastel*Palettes主唱'
        },
        {
          image: 'https://via.placeholder.com/800x600/FFD4E5/FFFFFF?text=Aya+Stage+2',
          title: '活力满满',
          desc: '用笑容感染每一个人'
        },
        {
          image: 'https://via.placeholder.com/800x600/FFC0E0/FFFFFF?text=Aya+Stage+3',
          title: '粉色梦想',
          desc: '永远追逐可爱的事物'
        },
        {
          image: 'https://via.placeholder.com/800x600/FFAAD5/FFFFFF?text=Aya+Stage+4',
          title: '偶像之路',
          desc: '与大家一起成长'
        }
      ],
      titleChars: '丸山彩 Maruyama Aya'.split(''),
      quotes: [
        '丸山添彩！',
        '只要不放弃，就一定能创造奇迹！',
        '……哎？还有时间吗！？呃、呃呃……我该说什么？',
        '希望我们的心意，能传达到那个座位',
        '就算是我，也绝不会输！'
      ],
      currentQuote: '丸山添彩'
    };
  },
  mounted() {
    setInterval(() => {
      this.currentQuote = this.quotes[Math.floor(Math.random() * this.quotes.length)];
    }, 5000);
  },
  methods: {
    handleCarouselChange(index) {
      this.currentQuote = this.quotes[index % this.quotes.length];
    }
  }
};
</script>

<style scoped>
.hero-section {
  min-height: 100vh;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.hero-background {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.gradient-orb {
  position: absolute;
  border-radius: 50%;
  filter: blur(8vw);
  opacity: 0.4;
  animation: float 20s ease-in-out infinite;
}

.orb-1 {
  width: 40vw;
  height: 40vw;
  background: radial-gradient(circle, #FFB6D9 0%, transparent 70%);
  top: -10vh;
  left: -10vw;
  animation-delay: 0s;
}

.orb-2 {
  width: 35vw;
  height: 35vw;
  background: radial-gradient(circle, #FF94CA 0%, transparent 70%);
  bottom: -10vh;
  right: -10vw;
  animation-delay: 7s;
}

.orb-3 {
  width: 30vw;
  height: 30vw;
  background: radial-gradient(circle, #FFD4E5 0%, transparent 70%);
  top: 30vh;
  right: 20vw;
  animation-delay: 14s;
}

@keyframes float {
  0%, 100% {
    transform: translate(0, 0) rotate(0deg);
  }
  33% {
    transform: translate(5vw, 10vh) rotate(120deg);
  }
  66% {
    transform: translate(-5vw, -5vh) rotate(240deg);
  }
}

.carousel-card {
  width: 100%;
  height: 100%;
  background-size: cover;
  background-position: center;
  border-radius: 2vw;
  overflow: hidden;
  position: relative;
  box-shadow: 0 1vw 3vw rgba(255, 148, 202, 0.4);
  transition: all 0.3s ease;
}

.carousel-card:hover {
  transform: translateY(-1vh);
  box-shadow: 0 2vw 4vw rgba(255, 148, 202, 0.6);
}

.carousel-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 50%;
  background: linear-gradient(to top, rgba(255, 148, 202, 0.9) 0%, transparent 100%);
}

.carousel-content {
  position: absolute;
  bottom: 5vh;
  left: 5vw;
  right: 5vw;
  color: white;
  z-index: 2;
}

.carousel-title {
  font-family: 'Pacifico', 'Noto Sans JP', cursive;
  font-size: 3vw;
  margin-bottom: 1vh;
  text-shadow: 0 0.3vw 1vw rgba(0, 0, 0, 0.3);
}

.carousel-desc {
  font-size: 1.2vw;
  opacity: 0.95;
  text-shadow: 0 0.2vw 0.5vw rgba(0, 0, 0, 0.3);
}

.sparkles {
  position: absolute;
  top: -3vh;
  right: 0;
  display: flex;
  gap: 1vw;
}

.sparkle {
  width: 1vw;
  height: 1vw;
  background: white;
  border-radius: 50%;
  animation: sparkle-twinkle 1.5s ease-in-out infinite;
}

.sparkle:nth-child(1) {
  animation-delay: 0s;
}

.sparkle:nth-child(2) {
  animation-delay: 0.3s;
}

.sparkle:nth-child(3) {
  animation-delay: 0.6s;
}

.sparkle:nth-child(4) {
  animation-delay: 0.9s;
}

.sparkle:nth-child(5) {
  animation-delay: 1.2s;
}

@keyframes sparkle-twinkle {
  0%, 100% {
    opacity: 0.3;
    transform: scale(0.8);
  }
  50% {
    opacity: 1;
    transform: scale(1.2);
  }
}

.hero-title {
  position: absolute;
  top: 10vh;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  z-index: 2;
}

.main-title {
  font-family: 'Pacifico', 'Noto Sans JP', cursive;
  font-size: 5vw;
  color: white;
  text-shadow: 0 0.5vw 2vw rgba(255, 148, 202, 0.8),
  0 0 3vw rgba(255, 148, 202, 0.6);
  margin-bottom: 2vh;
  display: flex;
  gap: 0.3vw;
}

.title-char {
  display: inline-block;
  animation: bounce-in 0.8s cubic-bezier(0.68, -0.55, 0.265, 1.55) both;
}

@keyframes bounce-in {
  0% {
    opacity: 0;
    transform: scale(0) rotateZ(-180deg);
  }
  50% {
    transform: scale(1.2) rotateZ(10deg);
  }
  100% {
    opacity: 1;
    transform: scale(1) rotateZ(0);
  }
}

.subtitle {
  font-size: 1.5vw;
  color: white;
  text-shadow: 0 0.3vw 1vw rgba(255, 148, 202, 0.8);
  animation: fade-slide-up 1s ease-out 0.5s both;
}

@keyframes fade-slide-up {
  from {
    opacity: 0;
    transform: translateY(2vh);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>