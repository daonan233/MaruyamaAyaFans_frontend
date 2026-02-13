<template>
  <section id="gallery" class="gallery-section">

    <div class="section-header">
      <h2 class="section-title">
        <span class="title-decoration">♡</span>
        精选卡面
        <span class="title-decoration">♡</span>
      </h2>
    </div>

    <div class="gallery-container">

      <!-- 左右渐隐遮罩 -->
      <div class="fade-left"></div>
      <div class="fade-right"></div>

      <div
          ref="track"
          :style="{ transform: `translateX(${translateX}px)` }"
          class="gallery-track"
      >
        <div
            v-for="(item, index) in renderList"
            :key="index"
            class="gallery-item"
        >
          <div
              :style="{ backgroundImage: `url(${item.url})` }"
              class="gallery-image"
          >
            <div class="gallery-overlay">
              <p>{{ item.title }}</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 高级玻璃控制条 -->
    <div class="control-bar">
      <div class="control-btn" @click="fastBackward"><<</div>
      <div class="control-btn pause" @click="togglePause">
        {{ paused ? '▶' : '⏸' }}
      </div>
      <div class="control-btn" @click="fastForward">>></div>
    </div>

  </section>
</template>

<script>
import {ref, computed, onMounted, onBeforeUnmount, nextTick} from 'vue'

export default {
  name: 'GallerySection',
  setup() {

    const galleryItems = [
      {url: '/gallery/zhaoxia.png', title: '被朝霞笼罩'},
      {url: '/gallery/muran.png', title: '慕然回首，只见青春'},
      {url: '/gallery/weixiao.png', title: '完美微笑'},
      {url: '/gallery/wanmei.png', title: '这样就完美了！'},
      {url: '/gallery/daxue.png', title: '享受☆大学生生活'},
      {url: '/gallery/gushi.png', title: '我们一起描绘的故事'},
      {url: '/gallery/tianxin.png', title: 'Sweeeet Bon Appétit'},
      {url: '/gallery/cuowu.png', title: 'Wrong Answer'},
      {url: '/gallery/biye.png', title: '毕业'},
      {url: '/gallery/hunli.png', title: '花嫁'},
    ]

    const renderList = computed(() => [...galleryItems, ...galleryItems])

    const track = ref(null)
    const translateX = ref(0)

    const normalSpeed = 0.4
    const fastSpeed = 2

    const speed = ref(-normalSpeed)
    const paused = ref(false)

    let animationId = null
    let trackWidth = 0

    const animate = () => {

      if (!paused.value) {
        translateX.value += speed.value

        if (speed.value < 0 && Math.abs(translateX.value) >= trackWidth / 2) {
          translateX.value = 0
        }

        if (speed.value > 0 && translateX.value >= 0) {
          translateX.value = -trackWidth / 2
        }
      }

      animationId = requestAnimationFrame(animate)
    }

    const fastForward = () => {
      paused.value = false
      speed.value = -fastSpeed
    }

    const fastBackward = () => {
      paused.value = false
      speed.value = fastSpeed
    }

    const togglePause = () => {
      paused.value = !paused.value
    }

    onMounted(async () => {
      await nextTick()
      trackWidth = track.value.scrollWidth
      animate()
    })

    onBeforeUnmount(() => {
      cancelAnimationFrame(animationId)
    })

    return {
      renderList,
      track,
      translateX,
      fastForward,
      fastBackward,
      togglePause,
      paused
    }
  }
}
</script>

<style scoped>

.gallery-section {
  padding: 8vh 5vw;
  background: linear-gradient(180deg, #fff0f7 0%, #ffd4e5 100%);
}

.section-header {
  text-align: center;
  margin-bottom: 5vh;
}

.section-title {
  font-size: 3vw;
  color: #ff5ca8;
  display: inline-flex;
  gap: 1vw;
}

.title-decoration {
  animation: spin 4s linear infinite;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

/* 展示区域更大 */
.gallery-container {
  position: relative;
  overflow: hidden;
  height: 55vh;
}

.gallery-track {
  display: flex;
  gap: 3vw;
  height: 100%;
  align-items: center;
  will-change: transform;
}

.gallery-item {
  flex-shrink: 0;
  width: 28vw;
  transition: transform 0.4s ease;
}

.gallery-item:hover {
  transform: scale(1.08);
}

.gallery-image {
  width: 100%;
  height: 45vh;
  background-size: cover;
  background-position: center;
  border-radius: 2vw;
  box-shadow: 0 1.5vw 4vw rgba(255, 120, 180, 0.4);
  position: relative;
  overflow: hidden;
}

.gallery-overlay {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 35%;
  background: linear-gradient(to top, rgba(255, 80, 160, 0.9), transparent);
  display: flex;
  align-items: flex-end;
  justify-content: center;
  padding-bottom: 3vh;
  color: white;
  font-size: 1.2vw;
  opacity: 0;
  transition: 0.3s;
}

.gallery-item:hover .gallery-overlay {
  opacity: 1;
}

/* 渐隐遮罩 */
.fade-left,
.fade-right {
  position: absolute;
  top: 0;
  width: 10vw;
  height: 100%;
  z-index: 5;
  pointer-events: none;
}

.fade-left {
  left: 0;
  background: linear-gradient(to right, #ffd4e5, transparent);
}

.fade-right {
  right: 0;
  background: linear-gradient(to left, #ffd4e5, transparent);
}

/* 控制条 */
.control-bar {
  margin-top: 5vh;
  display: flex;
  justify-content: center;
  gap: 3vw;
  backdrop-filter: blur(1vw);
}

.control-btn {
  width: 5vw;
  height: 5vw;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.6vw;
  cursor: pointer;
  background: linear-gradient(145deg, #ff89c4, #ff5ca8);
  color: white;
  box-shadow: 0 1vw 2vw rgba(255, 90, 170, 0.4);
  transition: all 0.3s ease;
}

.control-btn:hover {
  transform: scale(1.15);
}

.pause {
  width: 6vw;
  height: 6vw;
  font-size: 2vw;
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    box-shadow: 0 0 0 0 rgba(255, 90, 170, 0.6);
  }
  70% {
    box-shadow: 0 0 0 2vw rgba(255, 90, 170, 0);
  }
  100% {
    box-shadow: 0 0 0 0 rgba(255, 90, 170, 0);
  }
}

</style>
