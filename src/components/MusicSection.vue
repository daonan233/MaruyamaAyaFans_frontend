<template>
  <section id="music" class="music-section">
    <div class="music-player">

      <!-- 音频 -->
      <audio
          ref="audio"
          @ended="nextTrack"
          @timeupdate="updateProgress"
      ></audio>

      <!-- 实时频谱 -->
      <div class="player-visualizer">
        <div
            v-for="(bar, index) in visualizerData"
            :key="index"
            :style="{ height: bar + '%' }"
            class="visualizer-bar"
        ></div>
      </div>

      <div class="player-info">
        <div :class="{ paused: !isPlaying }" class="album-cover rotating">
          <img :src="currentTrack.cover"/>
        </div>

        <div class="track-info">
          <h3 class="track-title">{{ currentTrack.title }}</h3>
          <p class="track-artist">{{ currentTrack.artist }}</p>

          <!-- 进度条 -->
          <div class="progress-container">
            <div class="progress-bar" @click="seek">
              <div
                  :style="{ width: progress + '%' }"
                  class="progress"
              ></div>
              <div
                  :style="{ left: progress + '%' }"
                  class="progress-dot"
              ></div>
            </div>
            <div class="time">
              {{ formatTime(currentTime) }} / {{ formatTime(duration) }}
            </div>
          </div>
        </div>
      </div>

      <div class="player-controls">
        <button class="control-btn" @click="previousTrack">⏮</button>
        <button class="control-btn play-btn" @click="togglePlay">
          {{ isPlaying ? '⏸' : '▶' }}
        </button>
        <button class="control-btn" @click="nextTrack">⏭</button>
      </div>

      <!-- 歌词 -->
      <div class="lyrics-container">
        <div
            :style="{ transform: `translateY(-${lyricOffset}px)` }"
            class="lyrics"
        >
          <p
              v-for="(line, index) in lyrics"
              :key="index"
              :class="{ active: index === currentLyricIndex }"
          >
            {{ line.text }}
          </p>
        </div>

        <div class="fade-mask top"></div>
        <div class="fade-mask bottom"></div>
      </div>

      <!-- 歌曲列表 -->
      <div class="track-list">
        <div
            v-for="(track, index) in tracks"
            :key="index"
            :class="{ active: currentTrackIndex === index }"
            class="track-item"
            @click="selectTrack(index)"
        >
          <span class="track-number">{{ index + 1 }}</span>
          <span class="track-name">{{ track.title }}</span>
          <span class="track-duration">{{ track.duration }}</span>
        </div>
      </div>

    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      audioContext: null,
      analyser: null,
      dataArray: null,
      animationId: null,
      sourceNode: null,

      visualizerData: Array(60).fill(10),

      isPlaying: false,
      currentTrackIndex: 0,
      currentTime: 0,
      duration: 0,
      progress: 0,

      lyrics: [],
      currentLyricIndex: 0,

      tracks: [
        {
          title: 'しゅわりん☆どり～みん',
          artist: 'Pastel*Palettes',
          duration: '3:56',
          url: '/music/track1.mp3',
          cover: '/covers/track1.png',
          lrc: '/lyric/track1.lrc'
        },
        {
          title: '天下トーイツA to Z☆',
          artist: 'Pastel*Palettes',
          duration: '3:37',
          url: '/music/track2.mp3',
          cover: '/covers/track2.png',
          lrc: '/lyric/track2.lrc'
        },
        {
          title: 'きゅ～まい＊flower',
          artist: 'Pastel*Palettes',
          duration: '3:40',
          url: '/music/track3.mp3',
          cover: '/covers/track3.png',
          lrc: '/lyric/track3.lrc'
        },
        {
          title: 'ワクワクmeetsトリップ',
          artist: 'Pastel*Palettes',
          duration: '4:07',
          url: '/music/track4.mp3',
          cover: '/covers/track4.png',
          lrc: '/lyric/track4.lrc'
        },
        {
          title: 'ゆら・ゆらRing-Dong-Dance',
          artist: 'Pastel*Palettes',
          duration: '4:25',
          url: '/music/track5.mp3',
          cover: '/covers/track5.png',
          lrc: '/lyric/track5.lrc'
        }
      ]
    };
  },

  computed: {
    currentTrack() {
      return this.tracks[this.currentTrackIndex];
    },

    lyricOffset() {
      const lineHeight = 42;
      const containerHeight = 220;
      return (
          this.currentLyricIndex * lineHeight -
          (containerHeight / 2 - lineHeight / 2)
      );
    }
  },

  mounted() {
    this.initAudio();
    this.reloadTrack();
  },

  beforeDestroy() {
    cancelAnimationFrame(this.animationId);
  },

  methods: {
    initAudio() {
      const audio = this.$refs.audio;

      this.audioContext = new (window.AudioContext ||
          window.webkitAudioContext)();

      this.sourceNode =
          this.audioContext.createMediaElementSource(audio);

      this.analyser = this.audioContext.createAnalyser();
      this.analyser.fftSize = 128;
      this.analyser.smoothingTimeConstant = 0.8;

      this.sourceNode.connect(this.analyser);
      this.analyser.connect(this.audioContext.destination);

      this.dataArray = new Uint8Array(
          this.analyser.frequencyBinCount
      );

      this.animateVisualizer();
    },

    animateVisualizer() {
      if (!this.analyser) return;

      this.analyser.getByteFrequencyData(this.dataArray);

      const bars = 60;
      const step = Math.floor(this.dataArray.length / bars);

      this.visualizerData = Array.from({length: bars}, (_, i) => {
        const value = this.dataArray[i * step];
        return Math.max(5, (value / 255) * 100);
      });

      this.animationId = requestAnimationFrame(() =>
          this.animateVisualizer()
      );
    },

    async togglePlay() {
      const audio = this.$refs.audio;

      if (this.isPlaying) {
        audio.pause();
        this.isPlaying = false;
      } else {
        if (this.audioContext.state === 'suspended') {
          await this.audioContext.resume();
        }
        await audio.play();
        this.isPlaying = true;
      }
    },

    updateProgress() {
      const audio = this.$refs.audio;

      this.currentTime = audio.currentTime;
      this.duration = audio.duration || 0;

      this.progress =
          this.duration === 0
              ? 0
              : (this.currentTime / this.duration) * 100;

      this.updateLyrics();
    },

    seek(e) {
      const rect = e.currentTarget.getBoundingClientRect();
      const percent =
          (e.clientX - rect.left) / rect.width;

      this.$refs.audio.currentTime =
          percent * this.duration;
    },

    formatTime(time) {
      if (!time) return '0:00';
      const m = Math.floor(time / 60);
      const s = Math.floor(time % 60)
          .toString()
          .padStart(2, '0');
      return `${m}:${s}`;
    },

    nextTrack() {
      this.currentTrackIndex =
          (this.currentTrackIndex + 1) %
          this.tracks.length;
      this.reloadTrack();
    },

    previousTrack() {
      this.currentTrackIndex =
          (this.currentTrackIndex - 1 + this.tracks.length) %
          this.tracks.length;
      this.reloadTrack();
    },

    selectTrack(index) {
      this.currentTrackIndex = index;
      this.reloadTrack();
    },

    async reloadTrack() {
      const audio = this.$refs.audio;

      audio.pause();
      audio.currentTime = 0;

      this.currentTime = 0;
      this.progress = 0;
      this.currentLyricIndex = 0;
      this.lyrics = [];

      audio.src = this.currentTrack.url;
      audio.load();

      await this.loadLyrics();

      if (this.audioContext.state === 'suspended') {
        await this.audioContext.resume();
      }

      await audio.play();
      this.isPlaying = true;
    },

    async loadLyrics() {
      try {
        const res = await fetch(this.currentTrack.lrc);
        const text = await res.text();
        this.parseLRC(text);
      } catch (e) {
        this.lyrics = [];
      }
    },

    parseLRC(lrc) {
      const lines = lrc.split('\n');
      const result = [];

      lines.forEach(line => {
        const match = line.match(/\[(\d+):(\d+\.?\d*)\](.*)/);
        if (match) {
          const time =
              parseInt(match[1]) * 60 +
              parseFloat(match[2]);

          result.push({
            time,
            text: match[3].trim()
          });
        }
      });

      this.lyrics = result.sort((a, b) => a.time - b.time);
    },

    updateLyrics() {
      for (let i = 0; i < this.lyrics.length; i++) {
        if (
            this.currentTime >= this.lyrics[i].time &&
            (!this.lyrics[i + 1] ||
                this.currentTime < this.lyrics[i + 1].time)
        ) {
          this.currentLyricIndex = i;
          break;
        }
      }
    }
  }
};
</script>

<style scoped>

/* 原始全部样式 + 新增强化 */

/* ===== 外层 ===== */
.music-section {
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  border-radius: 3vw;
  margin: 5vh 3vw;
  box-shadow: 0 1vw 3vw rgba(0, 0, 0, 0.3);
  color: white;
  padding: 8vh 5vw;
  min-height: 60vh;
}

.music-player {
  max-width: 70vw;
  margin: 0 auto;
}

/* ===== 频谱 ===== */
.player-visualizer {
  display: flex;
  align-items: flex-end;
  justify-content: center;
  gap: 0.2vw;
  height: 18vh;
  margin-bottom: 5vh;
}

.visualizer-bar {
  width: 0.35vw;
  background: linear-gradient(to top, #FF94CA 0%, #FFB6D9 100%);
  border-radius: 0.5vw;
  box-shadow: 0 0 1.2vw #FFB6D9;
  transition: height 0.08s linear;
}

/* ===== 信息区 ===== */
.player-info {
  display: flex;
  align-items: center;
  gap: 3vw;
  margin-bottom: 5vh;
  padding: 3vh 3vw;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 2vw;
  backdrop-filter: blur(1vw);
}

.album-cover {
  width: 12vw;
  height: 12vw;
  border-radius: 50%;
  overflow: hidden;
  box-shadow: 0 0.8vw 2vw rgba(255, 148, 202, 0.5);
}

.album-cover img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.rotating {
  animation: rotate-album 12s linear infinite;
}

.rotating.paused {
  animation-play-state: paused;
}

@keyframes rotate-album {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.track-title {
  font-size: 2vw;
  margin-bottom: 1vh;
  color: #FFB6D9;
}

.track-artist {
  font-size: 1.2vw;
  opacity: 0.7;
}

/* ===== 控制按钮 ===== */
.player-controls {
  display: flex;
  justify-content: center;
  gap: 3vw;
  margin-bottom: 5vh;
}

.control-btn {
  width: 4vw;
  height: 4vw;
  border-radius: 50%;
  border: none;
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%);
  font-size: 1.5vw;
  color: white;
  cursor: pointer;
  box-shadow: 0 0.6vw 1.5vw rgba(255, 148, 202, 0.4);
  transition: all 0.3s ease;
}

.control-btn:hover {
  transform: scale(1.1);
}

.play-btn {
  width: 5vw;
  height: 5vw;
  font-size: 2vw;
}

/* ===== 进度条 ===== */
.progress-container {
  width: 100%;
  margin-top: 2vh;
}

.progress-bar {
  position: relative;
  height: 1vh;
  background: rgba(255, 255, 255, 0.15);
  border-radius: 2vh;
  cursor: pointer;
  overflow: hidden;
}

.progress {
  height: 100%;
  background: linear-gradient(90deg, #FF94CA, #FFB6D9, #FF94CA);
  background-size: 200% 100%;
  animation: progress-flow 3s linear infinite;
  border-radius: 2vh;
}

@keyframes progress-flow {
  0% {
    background-position: 0% 0;
  }
  100% {
    background-position: 200% 0;
  }
}

.progress-dot {
  position: absolute;
  top: 50%;
  transform: translate(-50%, -50%);
  width: 1.5vw;
  height: 1.5vw;
  background: white;
  border-radius: 50%;
  box-shadow: 0 0 1vw #FFB6D9;
}

.time {
  font-size: 1vw;
  margin-top: 1vh;
  opacity: 0.8;
}

/* ===== 歌词 ===== */
.lyrics-container {
  position: relative;
  height: 220px;
  overflow: hidden;
  margin: 4vh 0;
  text-align: center;
}

.lyrics {
  transition: transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.lyrics p {
  height: 42px;
  line-height: 42px;
  font-size: 1.2vw;
  opacity: 0.4;
  transition: all 0.3s ease;
  position: relative;
}

.lyrics p.active {
  font-size: 1.6vw;
  font-weight: bold;
  opacity: 1;
  transform: scale(1.05);
  background: linear-gradient(
      120deg,
      #FFD6EC 0%,
      #FFFFFF 40%,
      #FFD6EC 60%
  );
  background-size: 200% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: lyric-shine 3s linear infinite;
}

@keyframes lyric-shine {
  0% {
    background-position: -100% 0;
  }
  100% {
    background-position: 200% 0;
  }
}

/* ===== 渐隐遮罩 ===== */
.fade-mask {
  position: absolute;
  left: 0;
  width: 100%;
  height: 60px;
  pointer-events: none;
  z-index: 2;
}

.fade-mask.top {
  top: 0;
  background: linear-gradient(to bottom, #16213e 0%, rgba(22, 33, 62, 0) 100%);
}

.fade-mask.bottom {
  bottom: 0;
  background: linear-gradient(to top, #16213e 0%, rgba(22, 33, 62, 0) 100%);
}

/* ===== 歌曲列表 ===== */
.track-list {
  display: flex;
  flex-direction: column;
  gap: 1.5vh;
}

.track-item {
  display: flex;
  align-items: center;
  gap: 2vw;
  padding: 1.5vh 2vw;
  background: rgba(255, 255, 255, 0.05);
  border-radius: 1.5vw;
  cursor: pointer;
  transition: all 0.3s ease;
}

.track-item:hover {
  background: rgba(255, 182, 217, 0.2);
  transform: translateX(1vw);
}

.track-item.active {
  background: linear-gradient(
      90deg,
      rgba(255, 182, 217, 0.3),
      rgba(255, 148, 202, 0.3)
  );
  border-left: 0.4vw solid #FFB6D9;
}

.track-number {
  font-size: 1vw;
  color: #FFB6D9;
  width: 2vw;
  text-align: center;
}

.track-name {
  flex: 1;
  font-size: 1.1vw;
}

.track-duration {
  font-size: 0.9vw;
  opacity: 0.6;
}

</style>
