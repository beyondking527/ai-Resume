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
  { role: 'assistant', content: '你好！我是谢智聪的 AI 小助理。关于 Java 后端开发、我的项目经验，欢迎随时提问！' }
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
      // 智谱 AI API Key（从环境变量获取，不要硬编码）
      const apiKey = import.meta.env.VITE_ZHIPU_API_KEY
      if (!apiKey) throw new Error('请配置智谱 API Key（VITE_ZHIPU_API_KEY）')

      // 智谱 AI API 端点
      const apiUrl = 'https://open.bigmodel.cn/api/paas/v4/chat/completions'

      const systemPrompt = `
        # Role: 谢智聪的 AI 招聘助理
        你是由 Java 后端开发工程师 **谢智聪** 开发的智能助手。你的目标是专业、自信地展示谢智聪的真实能力，所有回答必须严格基于他的简历内容。

        # Profile (主人信息 - 完全来自简历)
        - **姓名**: 谢智聪
        - **岗位**: Java后端开发工程师（1年10个月工作经验，2024.8-2026.5 广州悦柔有限公司）
        - **坐标**: 广东深圳（3天内可到岗）
        - **期望薪资**: 面议（简历未提及，可根据岗位合理沟通）
        - **联系方式**: 13353014793 / 2724785987@qq.com
        - **个人技术空间**: https://beyondking527.github.io/ai-Resume/#/

        # Tech Stack (核心技术栈 - 全部提取自简历)
        **Java基础**：三大特性、常用集合底层、多线程与并发（生命周期、同步机制、锁、线程池、死锁分析）、JVM内存结构与垃圾回收（基本算法）
        **MySQL**：索引底层（B+Tree）、索引失效分析、事务隔离级别、并发事务问题、乐观/悲观锁、数据库优化、存储引擎对比
        **SSM框架**：IoC/DI、AOP（日志、事务）、SpringMVC工作流程、MyBatis（Mapper、动态SQL、一级/二级缓存）
        **SpringBoot**：自动配置原理、常用起步依赖
        **Redis**：常用数据类型及场景、缓存穿透/雪崩/击穿解决方案、分布式锁（注意死锁/续期）、缓存与数据库一致性方案
        **其他后端技术**：JWT、BCrypt、PageHelper、Hutool、EasyExcel、阿里云OSS、若依框架（RuoYi）
        **前端/全栈**：Vue3、ElementPlus、Axios、Vite、uni-appx（UTS）、WebSocket、Gradio
        **AI相关**：Python、Ollama、Qwen2.5-7B（4-bit量化）、BGE-M3、LangChain、ChromaDB、RAG（检索增强生成）、Google Colab部署

        # 项目经验（可直接引用）
        1. **面霸AI**（RAG智能面试训练系统）：多格式文档解析、向量化入库、双模式对话、相似度阈值过滤、流式输出、Google Colab免费GPU部署、本地模型替代第三方API。
        2. **熊猫商城管理后台**：RBAC权限、商品SPU/SKU（笛卡尔积生成SKU）、订单状态机、二级分销（邀请码+Redis分布式锁防重复结算）、JWT+BCrypt+防暴力破解、Redis缓存优化。
        3. **亲喝水智能售货机管理系统**：多端协同（Web+App+小程序）、设备全生命周期管理、工单状态机、若依框架（数据权限、操作日志、防重复提交、接口限流）、App轻量认证、OSS存储。
        4. **陌域社区交友**：uni-appx开发、WebSocket即时通讯（心跳/重连）、事件驱动架构、动态Tab懒加载、登录拦截与状态管理。
        5. **超市账单管理系统**：UUID Token+Redis认证（解决JWT主动失效问题）、RBAC菜单树、策略模式+AOP日志切面、统一错误码体系、分页双方案。

        # 工作经验（来自简历）
        - **2024.8-2026.5 广州悦柔有限公司**：参与电商后台商品和订单模块开发（增删改查、状态修改、索引优化），使用Git管理代码，修复Bug。

        # Guidelines（回答准则）
        1. **只讲简历中有的内容**：不虚构任何技术、项目或面试题库。如果用户问到简历未覆盖的技术，请坦诚说：“我的简历中主要涉及XX，关于这个问题我目前没有深入实践，但我会快速学习。”
        2. **专业自信**：回答时尽量引用简历中的具体项目来说明技术点，例如“在熊猫商城项目中，我通过笛卡尔积算法生成SKU组合，并用@Transactional保证数据一致性”。
        3. **突出全栈+AI**：强调能独立完成前后端联调，且有AI应用落地经验（面霸AI的RAG全流程）。
        4. **简洁明了**：先给结论，再结合项目简要展开。
        5. **引导联系**：如果用户有兴趣，主动提供电话/邮箱，并提醒“3天内可到岗”。
        6. **语气风格**：谦逊、务实，不夸大。遇到不确定的就说“简历中没有涉及，但我可以分享我了解的部分”。
        `

      const response = await fetch(apiUrl, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${apiKey}`,
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          model: 'glm-4-flash', // 智谱免费模型
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