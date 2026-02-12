<template>
  <section id="gallery" class="gallery-section">
    <div class="section-header">
      <h2 class="section-title">
        <span class="title-decoration">♡</span>
        精选卡面
        <span class="title-decoration">♡</span>
      </h2>
    </div>

    <div class="gallery-container" @mouseenter="pauseScroll = true" @mouseleave="pauseScroll = false">
      <div :class="{ paused: pauseScroll }" class="gallery-track">
        <div
            v-for="(item, index) in [...galleryItems, ...galleryItems]"
            :key="index"
            class="gallery-item"
            @click="$emit('open-viewer', item)"
        >
          <div :style="{ backgroundImage: `url(${item.url})` }" class="gallery-image">
            <div class="gallery-overlay">
              <div class="gallery-info">
                <p>{{ item.title }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'GallerySection',
  emits: ['open-viewer'],
  data() {
    return {
      pauseScroll: false,
      galleryItems: [
        {url: '/gallery/zhaoxia.png', title: '被朝霞笼罩'},
        {url: '/gallery/muran.png', title: '慕然回首，只见青春'},
        {url: '/gallery/weixiao.png', title: '完美微笑'},
        {url: '/gallery/wanmei.png', title: '这样就完美了！'},
        {url: '/gallery/daxxue.png', title: '享受☆大学生生活'},
        {url: '/gallery/gushi.png', title: '我们一起描绘的故事'},
        {url: '/gallery/tianxin.png', title: 'Sweeeet Bon Appétit'},
        {url: '/gallery/cuowu.png', title: 'Wrong Answer'},
        {url: '/gallery/biye.png', title: '毕业'},
        {url: '/gallery/hunli.png', title: '花嫁'},
      ]
    };
  }
};
</script>

<style scoped>
.gallery-section {
  background: linear-gradient(180deg, rgba(255, 240, 245, 0.5) 0%, rgba(255, 212, 229, 0.5) 100%);
  margin: 5vh 0;
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

.gallery-container {
  overflow: hidden;
  position: relative;
  padding: 3vh 0;
}

.gallery-track {
  display: flex;
  gap: 2vw;
  animation: scroll-gallery 30s linear infinite;
}

.gallery-track.paused {
  animation-play-state: paused;
}

@keyframes scroll-gallery {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-50%);
  }
}

.gallery-item {
  flex-shrink: 0;
  width: 20vw;
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.gallery-item:hover {
  transform: scale(1.1) rotate(3deg);
  z-index: 10;
}

.gallery-image {
  width: 100%;
  height: 28vh;
  background-size: cover;
  background-position: center;
  border-radius: 1.5vw;
  position: relative;
  overflow: hidden;
  box-shadow: 0 0.8vw 2vw rgba(255, 148, 202, 0.3);
}

.gallery-overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: linear-gradient(to top, rgba(255, 148, 202, 0.9) 0%, transparent 50%);
  opacity: 0;
  transition: opacity 0.3s ease;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding: 2vh;
}

.gallery-item:hover .gallery-overlay {
  opacity: 1;
}

.gallery-info {
  color: white;
  font-size: 1.1vw;
  font-weight: 600;
  text-align: center;
  text-shadow: 0 0.2vw 0.5vw rgba(0, 0, 0, 0.3);
}
</style>
