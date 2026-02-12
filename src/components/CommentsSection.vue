<template>
  <section id="comments" class="comments-section">
    <div class="section-header">
      <h2 class="section-title">
        <span class="title-decoration">✧</span>
        粉丝留言
        <span class="title-decoration">✧</span>
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
          show-word-limit
          type="textarea"
      />

      <el-button
          :loading="submitting"
          class="submit-btn"
          type="primary"
          @click="submitComment"
      >
        发送爱心 ♡
      </el-button>
    </div>

    <!-- 评论列表 -->
    <div class="comments-list">
      <div
          v-for="comment in comments"
          :key="comment.id"
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
            <span class="action-btn" @click="likeComment(comment)">
              <span class="heart">♡</span>
              {{ comment.likes }}
            </span>
          </div>
        </div>
      </div>
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
      try {
        const res = await axios.post(
            `http://localhost:3000/comments/${comment.id}/like`
        )

        comment.likes = res.data.likes
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
.comments-section {
  background: white;
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
  font-size: 3.5vw;
  color: #FF6FB5;
  display: inline-flex;
  align-items: center;
  gap: 1.5vw;
}

.comment-form {
  max-width: 50vw;
  margin: 0 auto 5vh;
  display: flex;
  flex-direction: column;
  gap: 2vh;
}

.submit-btn {
  background: linear-gradient(135deg, #FFB6D9 0%, #FF94CA 100%) !important;
  border-radius: 2vw !important;
  font-weight: bold !important;
}

.comments-list {
  display: flex;
  flex-direction: column;
  gap: 2vh;
  max-width: 60vw;
  margin: 0 auto;
}

.comment-card {
  display: flex;
  gap: 2vw;
  padding: 2vh 2vw;
  background: rgba(255, 240, 245, 0.5);
  border-radius: 1.5vw;
  transition: all 0.3s ease;
}

.comment-card:hover {
  background: rgba(255, 240, 245, 0.8);
  transform: translateX(0.5vw);
}

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
}

.comment-name {
  font-weight: bold;
  color: #FF6FB5;
}

.comment-time {
  font-size: 0.9vw;
  color: #999;
}

.comment-content {
  margin: 1vh 0;
}

.action-btn {
  cursor: pointer;
  color: #666;
}

.pagination-wrapper {
  margin-top: 4vh;
  text-align: center;
}
</style>
