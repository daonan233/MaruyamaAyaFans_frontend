<template>
  <section id="comments" class="comments-section">
    <!-- 流动背景装饰 -->
    <div class="background-decoration">
      <div class="floating-heart" style="left: 10%; animation-delay: 0s;">💕</div>
      <div class="floating-heart" style="left: 30%; animation-delay: 2s;">💖</div>
      <div class="floating-heart" style="left: 50%; animation-delay: 4s;">💗</div>
      <div class="floating-heart" style="left: 70%; animation-delay: 1s;">💝</div>
      <div class="floating-heart" style="left: 90%; animation-delay: 3s;">💓</div>
    </div>

    <div class="section-header">
      <h2 class="section-title">
        <span class="title-decoration sparkle">✧</span>
        留言墙
        <span class="title-decoration sparkle">✧</span>
      </h2>
    </div>

    <!-- 评论表单 -->
    <div class="comment-form">
      <el-input
          v-model="newComment.name"
          class="comment-input"
          maxlength="20"
          placeholder="你的昵称"
      >
        <template #prefix>
          <span class="input-icon">👤</span>
        </template>
      </el-input>

      <el-input
          v-model="newComment.content"
          :rows="4"
          class="comment-textarea"
          maxlength="200"
          placeholder="给彩彩留言吧～"
          resize="none"
          show-word-limit
          type="textarea"
      />

      <el-button
          :loading="submitting"
          class="submit-btn"
          type="primary"
          @click="submitComment"
      >
        <span class="btn-text">发送评论吧 ♡</span>
      </el-button>
    </div>

    <!-- 评论列表 -->
    <div class="comments-list">
      <transition-group name="comment-list">
        <div
            v-for="(comment, index) in comments"
            :key="comment.id"
            :style="{ animationDelay: index * 0.1 + 's' }"
            class="comment-card"
        >
          <div class="comment-avatar">
            <div :style="{ background: comment.color }" class="avatar-circle">
              {{ comment.name.charAt(0) }}
            </div>
          </div>

          <div class="comment-body">
            <div class="comment-header">
              <span class="comment-name">{{ comment.name }}</span>
              <span class="comment-time">{{ comment.time }}</span>
            </div>

            <div class="comment-content">
              {{ comment.content }}
            </div>

            <div class="comment-actions">
              <span
                  :class="{ 'liked': comment.liked }"
                  class="action-btn"
                  @click="likeComment(comment)"
              >
                <span class="heart">{{ comment.liked ? '❤' : '♡' }}</span>
                <span class="like-count">{{ comment.likes }}</span>
              </span>
            </div>
          </div>
        </div>
      </transition-group>
    </div>

    <!-- 分页 -->
    <div class="pagination-wrapper">
      <el-pagination
          :current-page="currentPage"
          :page-size="pageSize"
          :total="total"
          background
          layout="prev, pager, next"
          @current-change="handlePageChange"
      />
    </div>
  </section>
</template>

<script>
import {ElMessage} from 'element-plus'
import axios from 'axios'

export default {
  name: 'CommentsSection',

  data() {
    return {
      newComment: {
        name: '',
        content: ''
      },
      submitting: false,

      comments: [],
      total: 0,
      currentPage: 1,
      pageSize: 3
    }
  },

  mounted() {
    this.fetchComments()
  },

  methods: {
    async fetchComments() {
      try {
        const res = await axios.get('http://localhost:3000/comments', {
          params: {
            page: this.currentPage,
            pageSize: this.pageSize
          }
        })

        this.comments = res.data.data.map(item => ({
          ...item,
          liked: false,
          time: item.timeText,
          color: this.randomColor()
        }))

        this.total = res.data.total
      } catch (e) {
        ElMessage.error('获取评论失败')
      }
    },

    async submitComment() {
      if (!this.newComment.name || !this.newComment.content) {
        ElMessage.warning('请填写昵称和留言内容')
        return
      }

      this.submitting = true

      try {
        await axios.post('http://localhost:3000/comments', {
          name: this.newComment.name,
          content: this.newComment.content
        })

        this.newComment.name = ''
        this.newComment.content = ''

        this.currentPage = 1
        await this.fetchComments()

        ElMessage.success('留言成功！')
      } catch (e) {
        ElMessage.error('提交失败')
      }

      this.submitting = false
    },

    async likeComment(comment) {
      if (comment.liked) return

      try {
        const res = await axios.post(
            `http://localhost:3000/comments/${comment.id}/like`
        )

        comment.likes = res.data.likes
        comment.liked = true
      } catch (e) {
        ElMessage.error('点赞失败')
      }
    },

    handlePageChange(page) {
      this.currentPage = page
      this.fetchComments()
    },

    randomColor() {
      const colors = [
        'linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%)',
        'linear-gradient(135deg, #FFD4E5 0%, #FFB6D9 100%)',
        'linear-gradient(135deg, #FFC0E0 0%, #FFAAD5 100%)',
        'linear-gradient(135deg, #FFAAD5 0%, #FF94CA 100%)'
      ]
      return colors[Math.floor(Math.random() * colors.length)]
    }
  }
}
</script>

<style scoped>
/* 流动背景装饰 */
.background-decoration {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
  overflow: hidden;
  border-radius: 3vw;
}

.floating-heart {
  position: absolute;
  font-size: 2vw;
  animation: floatUp 8s infinite ease-in-out;
  opacity: 0.3;
}

@keyframes floatUp {
  0% {
    transform: translateY(100vh) rotate(0deg);
    opacity: 0;
  }
  10% {
    opacity: 0.3;
  }
  90% {
    opacity: 0.3;
  }
  100% {
    transform: translateY(-20vh) rotate(360deg);
    opacity: 0;
  }
}

/* 主容器 */
.comments-section {
  background: linear-gradient(135deg,
  #FFF5F8 0%,
  #FFE8F0 25%,
  #FFD4E5 50%,
  #FFE8F0 75%,
  #FFF5F8 100%);
  background-size: 400% 400%;
  animation: gradientFlow 15s ease infinite;
  border-radius: 3vw;
  margin: 5vh 3vw;
  box-shadow: 0 1vw 3vw rgba(255, 148, 202, 0.3),
  0 0 2vw rgba(255, 182, 217, 0.2);
  padding: 8vh 5vw;
  min-height: 60vh;
  position: relative;
}

@keyframes gradientFlow {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

/* 标题区域 */
.section-header {
  text-align: center;
  margin-bottom: 6vh;
  animation: slideDown 0.6s ease-out;
}

@keyframes slideDown {
  from {
    opacity: 0;
    transform: translateY(-3vh);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.section-title {
  font-size: 3.5vw;
  color: #FF6FB5;
  display: inline-flex;
  align-items: center;
  gap: 1.5vw;
  font-weight: bold;
  text-shadow: 0 0.3vw 1vw rgba(255, 148, 202, 0.3);
}

.title-decoration.sparkle {
  display: inline-block;
  animation: sparkle 2s infinite;
}

@keyframes sparkle {
  0%, 100% {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
  25% {
    transform: scale(1.3) rotate(15deg);
    opacity: 0.8;
  }
  50% {
    transform: scale(1) rotate(0deg);
    opacity: 1;
  }
  75% {
    transform: scale(1.3) rotate(-15deg);
    opacity: 0.8;
  }
}

/* 评论表单 */
.comment-form {
  max-width: 50vw;
  margin: 0 auto 5vh;
  display: flex;
  flex-direction: column;
  gap: 2vh;
  animation: fadeIn 0.8s ease-out 0.2s both;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(2vh);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.comment-input,
.comment-textarea {
  transition: all 0.3s ease;
  height: 5vh;
}

.comment-input:hover,
.comment-textarea:hover {
  transform: translateY(-0.3vh);
  box-shadow: 0 0.5vw 1.5vw rgba(255, 148, 202, 0.2);
}

.comment-input:focus-within,
.comment-textarea:focus-within {
  transform: translateY(-0.3vh);
  box-shadow: 0 0.5vw 2vw rgba(255, 111, 181, 0.3);
}

.input-icon {
  font-size: 1.5vw;
  animation: bounce 2s infinite;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-0.3vh);
  }
}

/* 提交按钮 */
.submit-btn {
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%) !important;
  width: 20vw;
  height: 5vh;
  border: 0;
  border-radius: 2vw !important;
  font-weight: bold !important;
  padding: 1.5vh 3vw !important;
  font-size: 1.2vw !important;
  transition: all 0.4s ease !important;
  box-shadow: 0 0.5vw 1.5vw rgba(255, 148, 202, 0.3);
  position: relative;
  overflow: hidden;
  display: block;
  margin: 5vh auto 0;
}


.submit-btn::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 0;
  height: 0;
  border-radius: 50%;
  background: rgba(255, 255, 255, 0.5);
  transform: translate(-50%, -50%);
  transition: width 0.6s, height 0.6s;
}

.submit-btn:hover::before {
  width: 300%;
  height: 300%;
}

.submit-btn:hover {
  transform: translateY(-0.5vh) scale(1.05) !important;
  box-shadow: 0 1vw 2.5vw rgba(255, 148, 202, 0.5) !important;
}

.submit-btn:active {
  transform: translateY(0) scale(0.98) !important;
}

.btn-text {
  position: relative;
  z-index: 1;
}

/* 评论列表 */
.comments-list {
  display: flex;
  flex-direction: column;
  gap: 2vh;
  max-width: 60vw;
  margin: 0 auto;
}

/* 列表过渡动画 */
.comment-list-enter-active {
  animation: slideInLeft 0.5s ease-out;
}

.comment-list-leave-active {
  animation: slideOutRight 0.5s ease-out;
}

@keyframes slideInLeft {
  from {
    opacity: 0;
    transform: translateX(-3vw);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

@keyframes slideOutRight {
  from {
    opacity: 1;
    transform: translateX(0);
  }
  to {
    opacity: 0;
    transform: translateX(3vw);
  }
}

/* 评论卡片 */
.comment-card {
  display: flex;
  gap: 2vw;
  padding: 2vh 2vw;
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(10px);
  border-radius: 1.5vw;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  border: 2px solid transparent;
  animation: cardAppear 0.6s ease-out both;
}

@keyframes cardAppear {
  from {
    opacity: 0;
    transform: translateY(2vh) scale(0.95);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.comment-card:hover {
  background: rgba(255, 255, 255, 0.95);
  transform: translateX(1vw) scale(1.02);
  box-shadow: 0 1vw 3vw rgba(255, 148, 202, 0.25);
  border-color: rgba(255, 148, 202, 0.3);
}

/* 头像 */
.avatar-circle {
  width: 4vw;
  height: 4vw;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  font-size: 1.8vw;
  font-weight: bold;
  box-shadow: 0 0.3vw 1vw rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  animation: avatarPulse 2s infinite;
}

@keyframes avatarPulse {
  0%, 100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
}

.comment-card:hover .avatar-circle {
  transform: rotate(360deg) scale(1.1);
  box-shadow: 0 0.5vw 1.5vw rgba(255, 148, 202, 0.4);
}

/* 评论内容 */
.comment-body {
  flex: 1;
}

.comment-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1vh;
}

.comment-name {
  font-weight: bold;
  color: #FF6FB5;
  font-size: 1.1vw;
  transition: all 0.3s ease;
}

.comment-card:hover .comment-name {
  color: #FF4D9E;
  transform: translateX(0.3vw);
}

.comment-time {
  font-size: 0.9vw;
  color: #999;
}

.comment-content {
  margin: 1vh 0;
  line-height: 1.6;
  color: #333;
  font-size: 1vw;
}

/* 点赞按钮 */
.comment-actions {
  margin-top: 1vh;
}

.action-btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5vw;
  cursor: pointer;
  color: #666;
  padding: 0.5vh 1vw;
  border-radius: 1vw;
  transition: all 0.3s ease;
  font-size: 1vw;
}

.action-btn:hover {
  background: rgba(255, 148, 202, 0.1);
  color: #FF6FB5;
  transform: translateY(-0.2vh);
}

.action-btn .heart {
  font-size: 1.3vw;
  transition: all 0.4s cubic-bezier(0.68, -0.55, 0.265, 1.55);
  display: inline-block;
}

.action-btn.liked .heart {
  animation: heartBeat 0.6s ease;
  color: #FF4D9E;
}

@keyframes heartBeat {
  0% {
    transform: scale(1);
  }
  20% {
    transform: scale(1.4) rotate(-10deg);
  }
  40% {
    transform: scale(1.2) rotate(10deg);
  }
  60% {
    transform: scale(1.4) rotate(-10deg);
  }
  80% {
    transform: scale(1.2) rotate(10deg);
  }
  100% {
    transform: scale(1);
  }
}

.action-btn.liked {
  color: #FF4D9E;
  pointer-events: none;
}

.like-count {
  font-weight: 500;
  transition: all 0.3s ease;
}

.action-btn.liked .like-count {
  animation: countPop 0.4s ease;
}

@keyframes countPop {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.3);
  }
  100% {
    transform: scale(1);
  }
}


/* 分页 */
.pagination-wrapper {
  margin-top: 4vh;
  display: flex;
  justify-content: center;
  animation: fadeIn 1s ease-out 0.4s both;
}

/* 分页按钮样式 */
.pagination-wrapper :deep(.el-pagination) {
  display: flex;
  justify-content: center;
}

.pagination-wrapper :deep(.el-pager li) {
  background-color: rgba(255, 255, 255, 0.8);
  color: #FF88BB;
  border-radius: 0.5vw;
  transition: all 0.3s ease;
}

.pagination-wrapper :deep(.el-pager li:hover) {
  color: #FF88BB;
  background-color: rgba(255, 136, 187, 0.2);
}

.pagination-wrapper :deep(.el-pager li.is-active) {
  background-color: #FF88BB !important;
  color: white !important;
}

.pagination-wrapper :deep(.btn-prev),
.pagination-wrapper :deep(.btn-next) {
  background-color: rgba(255, 255, 255, 0.8);
  color: #FF88BB;
  border-radius: 0.5vw;
  transition: all 0.3s ease;
}

.pagination-wrapper :deep(.btn-prev:hover),
.pagination-wrapper :deep(.btn-next:hover) {
  color: #FF88BB;
  background-color: rgba(255, 136, 187, 0.2);
}


/* 响应式优化 */
@media (max-width: 768px) {
  .comments-section {
    margin: 3vh 2vw;
    padding: 5vh 3vw;
  }

  .section-title {
    font-size: 5vw;
  }

  .comment-form {
    max-width: 90vw;
  }

  .comments-list {
    max-width: 90vw;
  }

  .avatar-circle {
    width: 8vw;
    height: 8vw;
    font-size: 3.5vw;
  }

  .floating-heart {
    font-size: 4vw;
  }
}
</style>