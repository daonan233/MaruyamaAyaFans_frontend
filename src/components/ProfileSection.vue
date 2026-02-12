<template>
  <section id="profile" class="profile-section">
    <div class="section-header">
      <h2 class="section-title">
        <span class="title-decoration">★</span>
        个人档案
        <span class="title-decoration">★</span>
      </h2>
    </div>

    <div class="profile-grid">
      <div v-for="(info, index) in profileInfo" :key="index" class="profile-card">
        <div class="card-icon">{{ info.icon }}</div>
        <div class="card-label">{{ info.label }}</div>
        <div class="card-value">{{ info.value }}</div>
      </div>
    </div>

    <div class="profile-bio">
      <div class="bio-text">
        <p>丸山之上缤纷彩！我是丸山彩～</p>
        <p>Pastel*Palettes主唱，慌不择路的自我检索者、软软乎乎的粉红担当...</p>
        <p>梦想着成为在舞台上闪闪发光，带给人梦想的偶像！</p>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'ProfileSection',
  data() {
    return {
      profileInfo: [
        {icon: '🎂', label: '生日', value: '12月27日'},
        {icon: '📏', label: '身高', value: '156cm'},
        {icon: '🎤', label: '所属团体', value: 'Pastel*Palettes'},
        {icon: '⭐', label: '担当', value: 'Vocal'},
        {icon: '🎨', label: '代表色', value: '#FF88BB'},
        {icon: '💖', label: '爱好', value: '可爱的东西'},
        {icon: '💖', label: '爱好', value: '可爱的东西'},
        {icon: '🍔', label: '喜欢吃的东西', value: '蛋包饭和汉堡肉之类的西餐'},
      ]
    };
  }
};
</script>

<style scoped>
.profile-section {
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.6) 0%, rgba(255, 240, 245, 0.6) 100%);
  border-radius: 3vw;
  margin: 5vh 3vw;
  box-shadow: 0 1vw 3vw rgba(255, 148, 202, 0.2);
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

.profile-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(15vw, 1fr));
  gap: 2vw;
  margin-bottom: 5vh;
}

.profile-card {
  background: white;
  padding: 3vh 2vw;
  border-radius: 2vw;
  text-align: center;
  box-shadow: 0 0.5vw 2vw rgba(255, 148, 202, 0.15);
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.profile-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.profile-card:hover::before {
  opacity: 0.1;
}

.profile-card:hover {
  transform: translateY(-1vh) scale(1.05);
  box-shadow: 0 1.5vw 3vw rgba(255, 148, 202, 0.3);
}

.card-icon {
  font-size: 3vw;
  margin-bottom: 1.5vh;
  position: relative;
  z-index: 1;
}

.card-label {
  font-size: 0.9vw;
  color: #888;
  margin-bottom: 1vh;
  position: relative;
  z-index: 1;
}

.card-value {
  font-size: 1.3vw;
  font-weight: 700;
  color: #FF6FB5;
  position: relative;
  z-index: 1;
}

.profile-bio {
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%);
  padding: 4vh 4vw;
  border-radius: 2vw;
  box-shadow: 0 0.8vw 2vw rgba(255, 148, 202, 0.3);
}

.bio-text {
  color: white;
  font-size: 1.2vw;
  line-height: 1.8;
  text-align: center;
}

.bio-text p {
  margin-bottom: 1.5vh;
}
</style>
