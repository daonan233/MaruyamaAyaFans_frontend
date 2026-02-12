<template>
  <div class="sidebar" :class="{ 'sidebar-collapsed': collapsed }">
    <div class="sidebar-toggle" @click="$emit('toggle')">
      <div class="toggle-icon">{{ collapsed ? '→' : '←' }}</div>
    </div>

    <div class="sidebar-content">
      <div class="sidebar-avatar">
        <div class="avatar-glow"></div>
        <img src="../assets/image_home/avatar.jpg" alt="修车丸山彩">
      </div>

      <nav class="sidebar-nav">
        <div
            v-for="(item, index) in navItems"
            :key="index"
            class="nav-item"
            :class="{ active: activeSection === item.id }"
            @click="$emit('navigate', item.id)"
        >
          <span class="nav-icon">{{ item.icon }}</span>
          <span class="nav-text">{{ item.name }}</span>
        </div>
      </nav>

      <div class="sidebar-footer">
        <div class="social-links">
          <div class="social-icon" v-for="social in socials" :key="social">{{ social }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'AyaSidebar',
  props: {
    collapsed: {
      type: Boolean,
      default: false
    },
    activeSection: {
      type: String,
      default: 'hero'
    }
  },
  emits: ['toggle', 'navigate'],
  data() {
    return {
      navItems: [
        { id: 'hero', name: '首页', icon: '🏠' },
        { id: 'profile', name: '档案', icon: '📋' },
        { id: 'gallery', name: '照片', icon: '📷' },
        { id: 'music', name: '音乐', icon: '🎵' },
        { id: 'interactive', name: '互动', icon: '✨' },
        { id: 'comments', name: '留言', icon: '💌' },
      ],
      socials: ['🐦', '📷', '🎬', '📺']
    };
  }
};
</script>

<style scoped>
.sidebar {
  position: fixed;
  left: 0;
  top: 0;
  width: 18vw;
  height: 100vh;
  background: linear-gradient(180deg, rgba(255, 182, 217, 0.95) 0%, rgba(255, 148, 202, 0.95) 100%);
  backdrop-filter: blur(1vw);
  border-right: 0.3vw solid rgba(255, 255, 255, 0.3);
  transition: all 0.5s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  z-index: 1000;
  box-shadow: 0.5vw 0 3vw rgba(255, 148, 202, 0.3);
  overflow: hidden;
}

.sidebar-collapsed {
  width: 5vw;
}

.sidebar-toggle {
  position: absolute;
  right: -2vw;
  top: 3vh;
  width: 3vw;
  height: 3vw;
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  box-shadow: 0 0.5vw 1.5vw rgba(255, 148, 202, 0.5);
  transition: all 0.3s ease;
  z-index: 10;
}

.sidebar-toggle:hover {
  transform: scale(1.1) rotate(180deg);
  box-shadow: 0 0.8vw 2vw rgba(255, 148, 202, 0.7);
}

.toggle-icon {
  font-size: 1.2vw;
  color: white;
  font-weight: bold;
}

.sidebar-content {
  padding: 3vh 1.5vw;
  height: 100%;
  display: flex;
  flex-direction: column;
  opacity: 1;
  transition: opacity 0.3s ease;
}

.sidebar-collapsed .sidebar-content {
  opacity: 0;
  pointer-events: none;
}

.sidebar-avatar {
  text-align: center;
  margin-bottom: 3vh;
  position: relative;
}

.avatar-glow {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 10vw;
  height: 10vw;
  background: radial-gradient(circle, rgba(255, 148, 202, 0.6) 0%, transparent 70%);
  border-radius: 50%;
  animation: glow-pulse 2s ease-in-out infinite;
}

@keyframes glow-pulse {
  0%, 100% { transform: translate(-50%, -50%) scale(1); opacity: 0.8; }
  50% { transform: translate(-50%, -50%) scale(1.2); opacity: 0.4; }
}

.sidebar-avatar img {
  width: 8vw;
  height: 8vw;
  border-radius: 50%;
  border: 0.3vw solid white;
  box-shadow: 0 0.5vw 2vw rgba(255, 148, 202, 0.5);
  position: relative;
  z-index: 1;
  transition: all 0.3s ease;
}

.sidebar-avatar img:hover {
  transform: scale(1.05) rotate(5deg);
}

.sidebar-avatar {
  text-align: center;
  margin-bottom: 3vh;
  position: relative;
}

/* 气泡主体 */
/* 气泡主体 */
.sidebar-avatar::after {
  content: "修哇修哇~";
  position: absolute;
  top: 0vw;
  right: -1.2vw;
  background: white;
  color: #ff94ca;
  padding: 0.6vw 1.2vw;
  border-radius: 1.5vw;
  font-size: 0.9vw;
  white-space: nowrap;
  box-shadow: 0 0.5vw 1.5vw rgba(0, 0, 0, 0.15);

  opacity: 0;
  transform: scale(0.8) translateY(10px);
  transform-origin: bottom left;
  transition: all 0.25s ease;
  pointer-events: none;
  z-index: 2;
}

/* 小尾巴 */
.sidebar-avatar::before {
  content: "";
  position: absolute;
  top: 4.8vh;
  right: 2.8vw;
  border-width: 0.7vw;
  border-style: solid;
  border-color: white transparent transparent transparent;
  transform: rotate(45deg);
  opacity: 0;
  transition: all 0.25s ease;
  pointer-events: none;
  z-index: 2;
}

/* hover 显示 */
.sidebar-avatar:hover::after,
.sidebar-avatar:hover::before {
  opacity: 1;
  transform: scale(1) translateY(0);
}


.sidebar-nav {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 1vh;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 1vw;
  padding: 1.5vh 1.5vw;
  border-radius: 1.5vw;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  color: white;
  font-weight: 500;
  font-size: 1vw;
  position: relative;
  overflow: hidden;
}

.nav-item::before {
  content: '';
  position: absolute;
  left: 0;
  top: 0;
  width: 0;
  height: 100%;
  background: rgba(255, 255, 255, 0.2);
  transition: width 0.3s ease;
  border-radius: 1.5vw;
}

.nav-item:hover::before {
  width: 100%;
}

.nav-item:hover {
  background: rgba(255, 255, 255, 0.15);
  transform: translateX(0.5vw);
}

.nav-item.active {
  background: rgba(255, 255, 255, 0.3);
  box-shadow: 0 0.3vw 1vw rgba(255, 148, 202, 0.3);
  transform: translateX(0.5vw);
}

.nav-icon {
  font-size: 1.5vw;
  position: relative;
  z-index: 1;
}

.nav-text {
  position: relative;
  z-index: 1;
}

.sidebar-footer {
  margin-top: auto;
  padding-top: 2vh;
  border-top: 0.2vw solid rgba(255, 255, 255, 0.3);
}

.social-links {
  display: flex;
  justify-content: space-around;
  gap: 0.5vw;
}

.social-icon {
  width: 3vw;
  height: 3vw;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.2);
  border-radius: 50%;
  font-size: 1.5vw;
  cursor: pointer;
  transition: all 0.3s ease;
}

.social-icon:hover {
  background: rgba(255, 255, 255, 0.4);
  transform: translateY(-0.3vw) rotate(10deg);
}
</style>