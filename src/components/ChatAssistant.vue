<template>
  <div class="chat-assistant">
    <div class="chat-messages" ref="messagesRef">
      <div
        v-for="(msg, index) in messages"
        :key="index"
        :class="['message', msg.role]"
      >
        <!-- AI 头像 -->
        <el-avatar 
          v-if="msg.role === 'assistant'"
          :size="36"
          :src="aiAvatar"
          class="message-avatar"
        />
        
        <!-- 消息内容区域：支持 Markdown 和代码高亮 -->
        <div class="message-content" v-html="renderContent(msg.content)"></div>
        
        <!-- 用户头像 -->
        <el-avatar 
          v-if="msg.role === 'user'"
          :size="36"
          src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png"
          class="message-avatar"
        />
      </div>
      
      <!-- 加载状态指示器 -->
      <div v-if="isLoading && messages[messages.length - 1]?.role === 'user'" class="message assistant loading-msg">
        <el-avatar :size="36" :src="aiAvatar" class="message-avatar" />
        <div class="typing-indicator">
          <span></span><span></span><span></span>
        </div>
      </div>
    </div>

    <div class="chat-input-area">
      <el-input
        v-model="inputMessage"
        type="textarea"
        :rows="2"
        placeholder="输入您的问题..."
        @keydown.enter.prevent="sendMessage"
      />
      <el-button
        type="primary"
        :loading="isLoading"
        @click="sendMessage"
      >
        发送
      </el-button>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue'
import { ElMessage } from 'element-plus'
import MarkdownIt from 'markdown-it'
import hljs from 'highlight.js'
import 'highlight.js/styles/atom-one-dark.css'
import aiAvatar from '@/assets/头像.jpg'

const md = new MarkdownIt({
  html: true,
  linkify: true,
  typographer: true,
  highlight: function (str, lang) {
    if (lang && hljs.getLanguage(lang)) {
      try {
        return '<pre class="hljs"><code>' +
               hljs.highlight(str, { language: lang, ignoreIllegals: true }).value +
               '</code></pre>'
      } catch (__) {}
    }
    return '<pre class="hljs"><code>' + md.utils.escapeHtml(str) + '</code></pre>'
  }
})

const messages = ref([
  { role: 'assistant', content: '你好！我是谢智聪的 AI 小助理。关于 Java 后端开发、Spring Cloud 微服务或我的项目经验，欢迎随时提问！' }
])
const inputMessage = ref('')
const isLoading = ref(false)
const messagesRef = ref(null)
const lastRequestTime = ref(0)
const MIN_REQUEST_INTERVAL = 2000

const renderContent = (content) => {
  if (!content) return ''
  return md.render(content)
}

const scrollToBottom = async () => {
  await nextTick()
  if (messagesRef.value) {
    messagesRef.value.scrollTop = messagesRef.value.scrollHeight
  }
}

const wait = (ms) => new Promise(resolve => setTimeout(resolve, ms))

const sendMessage = async () => {
  if (!inputMessage.value.trim() || isLoading.value) return

  const now = Date.now()
  const timeSinceLastRequest = now - lastRequestTime.value
  
  if (timeSinceLastRequest < MIN_REQUEST_INTERVAL) {
    ElMessage.warning('请稍后再试，避免频繁请求')
    return
  }

  const userMsg = inputMessage.value.trim()
  messages.value.push({ role: 'user', content: userMsg })
  inputMessage.value = ''
  await scrollToBottom()

  isLoading.value = true
  lastRequestTime.value = Date.now()

  messages.value.push({ role: 'assistant', content: '' })
  const assistantMsgIndex = messages.value.length - 1

  let retryCount = 0
  const maxRetries = 2

  while (retryCount <= maxRetries) {
    try {
      const apiKey = import.meta.env.VITE_DEEPSEEK_API_KEY
      if (!apiKey) throw new Error('请配置 API Key')

      const apiUrl = 'https://open.bigmodel.cn/api/paas/v4/chat/completions'

      const systemPrompt = `
      # Role: 谢智聪的 AI 招聘助理
      你是由 Java 后端开发工程师 **谢智聪** 开发的智能助手。你的目标是专业、自信地展示谢智聪的能力。

      # Profile (主人信息)
      - **姓名**: 谢智聪
      - **岗位**: Java后端开发 (2年经验)
      - **坐标**: 深圳市龙岗区 (3天内可到岗)
      - **期望薪资**: 8k~10k
      - **联系方式**: 13353014793 / 2724785987@qq.com

      # Tech Stack (核心技术栈)
      1. **核心语言**: 精通 Java，熟悉 Python/JS/C。
      2. **后端框架**: 熟练掌握 SpringBoot, MyBatis/MyBatisPlus, SpringCloud (Nacos, OpenFeign)。
      3. **数据存储**: 熟练使用 MySQL (SQL优化), Redis (缓存机制/SpringCache整合)。
      4. **中间件/云服务**: 熟悉 RabbitMQ 消息队列, 阿里云 OSS, 华为云 IoT (MQTT协议)。
      5. **工程化能力**: 熟练 Git, Linux, Docker 容器化部署, Nginx 反向代理。
      6. **前沿探索**: 具备 AI 应用开发经验 (RAG知识库, MCP系统对接, 结构化提示词设计)。

      # Guidelines (回答准则)
      1. **专业自信**: 回答技术问题时，体现对底层原理的理解。
      2. **突出优势**: 强调"全栈视野"、"AI 融合开发能力"以及"高可用接口设计经验"。
      3. **简洁明了**: 避免冗长，直接给出结论。
      4. **引导联系**: 如果用户感兴趣，主动提供电话或邮箱，并提醒"3天内即可到岗"。
      5. **语气风格**: 谦逊但充满干劲。
    `

      const response = await fetch(apiUrl, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${apiKey}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          model: 'glm-4.7-flash',
          stream: true,
          messages: [
            { role: 'system', content: systemPrompt },
            ...messages.value.filter(m => m.role !== 'assistant' || m.content !== '')
          ]
        })
      })

      if (response.status === 429) {
        retryCount++
        if (retryCount <= maxRetries) {
          const waitTime = Math.pow(2, retryCount) * 1000
          ElMessage.warning(`请求过于频繁，${waitTime / 1000}秒后自动重试...`)
          await wait(waitTime)
          continue
        } else {
          throw new Error('API 调用频率超限，请稍后再试')
        }
      }

      if (!response.ok) throw new Error(`请求失败: ${response.status}`)

      const reader = response.body.getReader()
      const decoder = new TextDecoder()
      let buffer = ''

      while (true) {
        const { done, value } = await reader.read()
        if (done) break

        const chunk = decoder.decode(value, { stream: true })
        buffer += chunk

        const lines = buffer.split('\n')
        buffer = lines.pop() || ''

        for (const line of lines) {
          if (line.startsWith('data: ')) {
            const jsonStr = line.slice(6)
            if (jsonStr === '[DONE]') continue

            try {
              const data = JSON.parse(jsonStr)
              const delta = data.choices[0]?.delta?.content
              if (delta) {
                messages.value[assistantMsgIndex].content += delta
                await scrollToBottom()
              }
            } catch (e) {
              buffer = line + '\n' + buffer
            }
          }
        }
      }
      
      break
    } catch (error) {
      if (retryCount < maxRetries && error.message.includes('429')) {
        continue
      }
      ElMessage.error(error.message || '发送失败')
      messages.value.splice(assistantMsgIndex, 1)
      break
    } finally {
      isLoading.value = false
    }
  }
}
</script>

<style lang="scss" scoped>
.chat-assistant {
  display: flex;
  flex-direction: column;
  height: 550px;
  border: 1px solid #e4e7ed;
  border-radius: 16px;
  overflow: hidden;
  background: #fff;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
}

.chat-messages {
  flex: 1;
  padding: 20px;
  overflow-y: auto;
  background: #fafafa;

  .message {
    margin-bottom: 20px;
    display: flex;
    align-items: flex-start;
    gap: 12px;
    max-width: 100%;

    &.user {
      flex-direction: row-reverse;

      .message-content {
        background: #409eff;
        color: #fff;
        border-radius: 18px 4px 18px 18px;
        
        :deep(code) {
          background: rgba(255, 255, 255, 0.2);
          color: #fff;
        }
      }
    }

    &.assistant {
      .message-content {
        background: #fff;
        color: #303133;
        border-radius: 4px 18px 18px 18px;
        border: 1px solid #ebeef5;
        box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);

        :deep(strong) { font-weight: 700; color: #2c3e50; }
        :deep(ul), :deep(ol) { padding-left: 20px; margin: 8px 0; }
        :deep(li) { margin-bottom: 4px; line-height: 1.6; }
        :deep(p) { margin: 0 0 8px 0; &:last-child { margin-bottom: 0; } }
        :deep(code) {
          background: #f2f4f7;
          padding: 2px 6px;
          border-radius: 4px;
          font-family: 'Consolas', monospace;
          font-size: 0.9em;
          color: #e83e8c;
        }
        :deep(pre) {
          background: #282c34;
          border-radius: 8px;
          padding: 12px;
          overflow-x: auto;
          margin: 10px 0;
          code {
            background: transparent;
            color: #abb2bf;
            padding: 0;
          }
        }
      }
    }

    .message-avatar {
      flex-shrink: 0;
      border: 2px solid #fff;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }

    .message-content {
      max-width: 75%;
      padding: 12px 18px;
      font-size: 15px;
      line-height: 1.6;
      word-break: break-word;
    }
  }

  .typing-indicator {
    background: #fff;
    padding: 12px 18px;
    border-radius: 4px 18px 18px 18px;
    border: 1px solid #ebeef5;
    display: flex;
    gap: 4px;
    
    span {
      width: 6px;
      height: 6px;
      background: #909399;
      border-radius: 50%;
      animation: bounce 1.4s infinite ease-in-out both;
      
      &:nth-child(1) { animation-delay: -0.32s; }
      &:nth-child(2) { animation-delay: -0.16s; }
    }
  }
}

@keyframes bounce {
  0%, 80%, 100% { transform: scale(0); }
  40% { transform: scale(1); }
}

.chat-input-area {
  display: flex;
  gap: 12px;
  padding: 16px 20px;
  border-top: 1px solid #e4e7ed;
  background: #fff;

  :deep(.el-textarea__inner) {
    resize: none;
    border-radius: 8px;
    background: #f5f7fa;
    &:focus { background: #fff; }
  }

  .el-button {
    align-self: flex-end;
    border-radius: 8px;
    padding: 10px 20px;
  }
}
</style>