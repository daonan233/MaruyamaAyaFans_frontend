<template>
  <div class="aya-fan-page">
    <!-- 侧边栏 -->
    <AyaSidebar
        :collapsed="sidebarCollapsed"
        :active-section="activeSection"
        @toggle="toggleSidebar"
        @navigate="scrollToSection"
    />

    <!-- 主内容区 -->
    <div class="main-content" :class="{ 'content-expanded': sidebarCollapsed }">
      <!--轮播图区 -->
      <HeroSection />
      <!-- 个人档案 -->
      <ProfileSection />
      <!-- 照片墙 -->
      <GallerySection @open-viewer="openImageViewer" />

      <!-- 音乐播放器 -->
      <MusicSection />
      <!-- 互动彩蛋 -->
      <InteractiveSection />
      <!-- 留言板 -->
      <CommentsSection />

      <!-- 页脚 -->
      <footer class="footer">
        <div class="footer-content">
          <div class="footer-sparkles">
            <div class="footer-sparkle" v-for="n in 10" :key="n"></div>
          </div>
          <p class="footer-text">Made with ♡ for Maruyama Aya Fans</p>
          <p class="footer-copyright">© 2026 丸山彩粉丝主页 | Powered by daonan</p>
        </div>
      </footer>
    </div>

    <!-- 图片查看器对话框 -->
    <el-dialog
        v-model="imageViewerVisible"
        width="80vw"
        :show-close="false"
        class="image-viewer-dialog"
    >
      <div class="image-viewer">
        <img :src="selectedImage?.url" :alt="selectedImage?.title">
        <div class="viewer-info">{{ selectedImage?.title }}</div>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';
import AyaSidebar from '../components/AyaSidebar.vue';
import HeroSection from '../components/HeroSection.vue';
import ProfileSection from '../components/ProfileSection.vue';
import GallerySection from '../components/GallerySection.vue';
import CommentsSection from '../components/CommentsSection.vue';
import MusicSection from '../components/MusicSection.vue';
import InteractiveSection from '../components/InteractiveSection.vue';

export default {
  name: 'MaruyamaAyaFans',
  components: {
    AyaSidebar,
    HeroSection,
    ProfileSection,
    GallerySection,
    CommentsSection,
    MusicSection,
    InteractiveSection
  },
  setup() {
    const sidebarCollapsed = ref(false);
    const activeSection = ref('hero');
    const imageViewerVisible = ref(false);
    const selectedImage = ref(null);

    const toggleSidebar = () => {
      sidebarCollapsed.value = !sidebarCollapsed.value;
    };

    const scrollToSection = (id) => {
      activeSection.value = id;
      const element = document.getElementById(id);
      if (element) {
        element.scrollIntoView({ behavior: 'smooth', block: 'start' });
      }
    };

    const openImageViewer = (item) => {
      selectedImage.value = item;
      imageViewerVisible.value = true;
    };

    const handleScroll = () => {
      const sections = ['hero', 'profile', 'gallery', 'comments', 'music', 'interactive'];
      const scrollPosition = window.scrollY + window.innerHeight / 3;

      for (const section of sections) {
        const element = document.getElementById(section);
        if (element) {
          const { offsetTop, offsetHeight } = element;
          if (scrollPosition >= offsetTop && scrollPosition < offsetTop + offsetHeight) {
            activeSection.value = section;
            break;
          }
        }
      }
    };

    onMounted(() => {
      window.addEventListener('scroll', handleScroll);
    });

    onUnmounted(() => {
      window.removeEventListener('scroll', handleScroll);
    });

    return {
      sidebarCollapsed,
      activeSection,
      imageViewerVisible,
      selectedImage,
      toggleSidebar,
      scrollToSection,
      openImageViewer
    };
  }
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@300;400;700;900&family=Pacifico&family=Quicksand:wght@300;500;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Quicksand', 'Noto Sans JP', sans-serif;
  background: linear-gradient(135deg, #FFF0F5 0%, #FFE5F0 50%, #FFD4E5 100%);
  color: #4A4A4A;
  overflow-x: hidden;
}
</style>

<style scoped>
.aya-fan-page {
  min-height: 100vh;
  display: flex;
}

.main-content {
  margin-left: 18vw;
  width: calc(100vw - 18vw);
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.content-expanded {
  margin-left: 5vw;
  width: calc(100vw - 5vw);
}

/* 页脚 */
.footer {
  background: linear-gradient(135deg, #FF6FB5 0%, #FF94CA 100%);
  padding: 5vh 5vw;
  text-align: center;
  color: white;
  position: relative;
  overflow: hidden;
}

.footer-sparkles {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
}

.footer-sparkle {
  position: absolute;
  width: 0.5vw;
  height: 0.5vw;
  background: white;
  border-radius: 50%;
  animation: footer-twinkle 3s ease-in-out infinite;
}

.footer-sparkle:nth-child(1) { top: 20%; left: 10%; animation-delay: 0s; }
.footer-sparkle:nth-child(2) { top: 40%; left: 30%; animation-delay: 0.5s; }
.footer-sparkle:nth-child(3) { top: 60%; left: 50%; animation-delay: 1s; }
.footer-sparkle:nth-child(4) { top: 80%; left: 70%; animation-delay: 1.5s; }
.footer-sparkle:nth-child(5) { top: 30%; left: 90%; animation-delay: 2s; }
.footer-sparkle:nth-child(6) { top: 70%; left: 20%; animation-delay: 2.5s; }
.footer-sparkle:nth-child(7) { top: 50%; left: 60%; animation-delay: 0.8s; }
.footer-sparkle:nth-child(8) { top: 25%; left: 75%; animation-delay: 1.3s; }
.footer-sparkle:nth-child(9) { top: 65%; left: 35%; animation-delay: 1.8s; }
.footer-sparkle:nth-child(10) { top: 85%; left: 85%; animation-delay: 2.3s; }

@keyframes footer-twinkle {
  0%, 100% { opacity: 0.2; transform: scale(0.5); }
  50% { opacity: 1; transform: scale(1.5); }
}

.footer-content {
  position: relative;
  z-index: 1;
}

.footer-text {
  font-family: 'Pacifico', cursive;
  font-size: 1.8vw;
  margin-bottom: 1vh;
  text-shadow: 0 0.3vw 0.8vw rgba(0, 0, 0, 0.3);
}

.footer-copyright {
  font-size: 1vw;
  opacity: 0.9;
}

/* 图片查看器 */
.image-viewer-dialog :deep(.el-dialog) {
  background: rgba(0, 0, 0, 0.9) !important;
}

.image-viewer {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.image-viewer img {
  max-width: 100%;
  max-height: 70vh;
  border-radius: 1vw;
  box-shadow: 0 1vw 3vw rgba(255, 148, 202, 0.5);
}

.viewer-info {
  margin-top: 3vh;
  color: white;
  font-size: 1.5vw;
  text-align: center;
}

/* 响应式调整 */
@media (max-width: 768px) {
  .main-content {
    margin-left: 25vw;
    width: calc(100vw - 25vw);
  }

  .content-expanded {
    margin-left: 8vw;
    width: calc(100vw - 8vw);
  }
}
</style>