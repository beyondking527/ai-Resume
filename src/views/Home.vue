<template>
  <div class="home-page">
    <!-- 背景图层 -->
    <div class="background-layer"></div>
    <div class="overlay-layer"></div>

    <!-- 导航栏 -->
    <el-header class="site-header">
      <div class="header-content">
        <div class="brand">
          <h1 class="site-title">Xie's Space</h1>
        </div>
      </div>
    </el-header>

    <!-- 主要内容区 -->
    <el-main class="site-main">
      <!-- Hero 区域 -->
      <section class="hero-section">
        <div class="hero-container">
          <div class="hero-text">
            <h2>构建智能未来</h2>
            <p>全栈开发工程师 · 专注于企业级应用与智能化解决方案</p>
            <div class="social-links">
              <el-tag type="primary" effect="dark" round>Java 后端开发</el-tag>
              <el-tag type="success" effect="dark" round>Vue3 前端技术</el-tag>
              <el-tag type="warning" effect="dark" round>系统架构设计</el-tag>
            </div>
          </div>
          <div class="hero-image-wrapper">
            <el-avatar :size="280" :src="userAvatar" class="hero-avatar" />
          </div>
        </div>
      </section>

      <!-- 作品展示区域 -->
      <section class="works-section">
        <div class="section-title">
          <h3>精选作品</h3>
          <span class="section-subtitle">My Recent Projects</span>
        </div>

        <div class="projects-container">
          <ProjectCard v-for="(project, index) in projects" :key="index" :project="project" />
        </div>
      </section>
    </el-main>

    <!-- 页脚 -->
    <el-footer class="site-footer">
      <p>© 2026 JOSE. All Rights Reserved.</p>
    </el-footer>

    <!-- AI 小助理悬浮按钮 -->
    <div class="ai-float-container">
      <div class="ai-label">AI 小助理</div>
      <div class="ai-float-trigger" @click="toggleChat">
        <el-icon :size="28">
          <ChatDotRound />
        </el-icon>
      </div>
    </div>

    <!-- AI 聊天抽屉/弹窗 -->
    <transition name="chat-slide">
      <div v-if="isChatVisible" class="chat-popup">
        <div class="chat-header">
          <span>AI 小助理</span>
          <el-icon class="close-btn" @click="toggleChat">
            <Close />
          </el-icon>
        </div>
        <ChatAssistant />
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { ChatDotRound, Close } from '@element-plus/icons-vue'
import ProjectCard from '@/components/ProjectCard.vue'
import ChatAssistant from '@/components/ChatAssistant.vue'
import userAvatar from '@/assets/真人头像.jpg'

const shopImages = import.meta.glob('@/assets/imagesProject/shopadmin/*.png', { eager: true })
const shopImgList = Object.values(shopImages).map(module => module.default)

const vendingImages = import.meta.glob('@/assets/imagesProject/vending/*.png', { eager: true })
const vendingImgList = Object.values(vendingImages).map(module => module.default)

const supermarketImages = import.meta.glob('@/assets/imagesProject/supermarket/*.png', { eager: true })
const supermarketImgList = Object.values(supermarketImages).map(module => module.default)

const communityImages = import.meta.glob('@/assets/imagesProject/community/*.png', { eager: true })
const communityImgList = Object.values(communityImages).map(module => module.default)

const AIinterviewImages = import.meta.glob('@/assets/imagesProject/AI-interview/*.png', { eager: true })
const AIinterviewImgList = Object.values(AIinterviewImages).map(module => module.default)

const isChatVisible = ref(false)

const toggleChat = () => {
  isChatVisible.value = !isChatVisible.value
}

const projects = ref([
  {
    title: '商城管理后台',
    category: '电商管理平台',
    tagType: 'primary',
    images: shopImgList,
    description: '基于 Spring Boot 2.7 + Vue3 开发的现代化商城管理后台系统，采用前后端分离架构。系统实现了完整的 RBAC 权限管理、商品 SKU/SPU 多维度管理、订单全生命周期管理、二级分销体系等核心业务模块。支持 Docker 容器化部署，集成 Redis 缓存、JWT 认证、阿里云 OSS 等技术，提供完善的监控告警和 CI/CD 自动化部署方案。',
    technologies: ['Spring Boot 2.7', 'MyBatis-Plus 3.5', 'MySQL 8.0', 'Redis', 'Vue 3', 'Element Plus', 'JWT', 'Docker', 'Nginx'],
    features: [
      '完整的 RBAC 权限管理体系，支持动态路由和按钮级权限控制',
      '商品 SKU/SPU 多维度管理，支持规格笛卡尔积算法自动生成',
      '订单状态机设计，覆盖待付款、待发货、已发货、已完成、退款等完整流程',
      '二级分销系统，支持佣金自动计算和推广关系管理',
      '集成 Redis 缓存机制，优化高频查询性能',
      'Docker 容器化部署，支持 Docker Compose 编排和 CI/CD 自动化',
      '完善的日志管理和监控告警体系（ELK + Prometheus + Grafana）',
      '支持阿里云 OSS 文件存储，提供图片分类管理和批量上传功能'
    ]
  },
  {
    "title": "面霸AI",
    "category": "智能面试模拟助手（云端自部署版）",
    "tagType": "success",
    "images": AIinterviewImgList,
    "description": "基于 RAG 技术、完全运行于 Google Colab 免费 GPU 的智能面试模拟助手。采用 Python + Gradio + Ollama + ChromaDB 技术栈，利用本地 Qwen2.5-7B 对话模型和 BGE-M3 嵌入模型，取代智谱 API，实现零成本、离线、数据安全的面试练习。系统包含双模式（教练模式5段式结构化回答、点评模式10分制评分）、随机出题、多格式知识库导入、流式输出等核心功能。支持 JSON/PDF/DOCX/XLSX/TXT/MD 六种格式，具有文本重叠切分、相似度阈值过滤、对话历史自动截断、中文数据库名哈希映射等优化特性。通过 Gradio share 生成公网链接，可随时随地访问。",
    "technologies": ["Python 3.12", "Gradio 6.0+", "Ollama (本地大模型运行时)", "Qwen2.5-7B-Instruct (4-bit 量化)","BGE-M3 (嵌入模型)","ChromaDB 0.4+","Google Colab (免费 T4 GPU)","LangChain (RAG 流程集成)","pdfminer.six / python-docx / openpyxl"],
    "features": [
      "完全脱离第三方 API，基于 Ollama 本地模型实现零成本、高隐私的面试模拟",
      "利用 Google Colab 免费 GPU 运行 7B 量化模型，并生成公网 Gradio 链接，随时随地访问",
      "完整 RAG 流程：文本重叠切分 → BGE 向量化 → ChromaDB 检索 → 相似度阈值过滤 → Qwen 生成",
      "双模式面试：教练模式输出5段式结构化参考答案，点评模式进行10分制专业评分",
      "流式输出：对接 Ollama 流式接口，实现逐字打字机效果",
      "多格式知识库导入：支持 JSON / PDF / DOCX / XLSX / TXT / MD，Markdown 文件自动按标题切分",
      "双历史对话管理：分离 API 内部历史与界面显示历史，保证上下文完整与界面简洁",
      "中文数据库名自动哈希映射，突破 ChromaDB 命名限制，UI 友好显示",
      "对话历史自动截断，保留最近10轮对话，防止超出模型上下文窗口"
    ]
  },
  {
    title: '亲喝水智能售货机管理系统',
    category: '智慧零售',
    tagType: 'success',
    images: vendingImgList,
    description: '面向智能售货机运营场景的多端协同管理平台，基于若依3.8.7前后端分离架构二次开发。系统包含三大端：运营管理后台（PC端，Vue3+Spring Boot+Spring Security）、运维运营人员App（移动端，短信验证码登录+工单处理）、C端用户小程序（商品浏览+策略定价+在线支付），三端共享MySQL+Redis实现数据实时协同。提供设备全生命周期管理、智能工单流转、货道库存管理、策略定价等核心功能。',
    technologies: ['Spring Boot', 'Vue 3', '若依框架', 'MyBatis', 'MyBatis-Plus', 'MySQL', 'Redis', 'Druid', 'Spring Security', 'JWT', '阿里云OSS', '阿里云短信', 'Pinia', 'Vite'],
    features: [
      '基于若依3.8.7二开：整合Spring Boot + MyBatis + Spring Security + Druid连接池，支持慢SQL记录与XSS防护',
      'JWT+Redis双认证方案：后台Token携带UUID、用户信息缓存Redis，支持主动失效与自动续期；App端纯JWT方案(7天有效)，Token自携带用户信息',
      '多端协同架构：运营后台(Vue3+Spring Security)、运维App(MyBatis-Plus+短信登录+ThreadLocal)、用户小程序(策略定价+在线支付)三端协同工作、共享数据库',
      '设备全生命周期管理：新增设备自动生成编号与货道(型号行列配置)、货道关联商品、策略分配、状态流转(未投放→运营→撤机)，@Transactional保证设备与货道一致性',
      '智能工单系统：投放/补货/维修/撤机四类工单，Redis自增生成编号(日期+4位序号)，创建时多重校验(设备状态/重复工单/员工区域)，App端完成工单后联动更新：补货工单→货道库存累加，投放工单→设备状态改为运营',
      'AOP切面零侵入：框架层提供@DataScope数据权限(动态拼接SQL)、@Log操作日志(异步线程池)、@RateLimiter接口限流，业务代码无需关心横切逻辑',
      '价格策略：每台售货机可绑定不同价格策略，小程序查询商品时动态计算折扣价格(realPrice=price×discount/100)，后台可灵活调整不同设备的定价',
      '前端深度封装：Axios请求拦截(Token注入+防重复提交)、Vue Router动态路由(import.meta.glob+addRoute)、v-hasPermi按钮权限指令、字典组件自动映射',
      '阿里云生态集成：OSS云存储(X-File-Starter统一接口)、短信SDK验证码登录(Redis存储5分钟有效，登录后删除)',
      '货道库存优化：补货工单完成时一次性查询所有货道，Stream内存匹配累加库存后批量更新，避免循环查询N+1问题'
    ]
  },
  {
    title: '社区交友 App',
    category: '社交应用',
    tagType: 'warning',
    images: communityImgList,
    description: '面向兴趣社交的社区交友应用，支持帖子发布与浏览、评论互动、私信聊天、话题分类等功能。基于uni-app跨端开发，WebSocket实现即时通讯，支持Android/iOS双端运行。',
    technologies: ['UniApp', 'Vue3', 'WebSocket', 'UTS', 'Pinia'],
    features: [
      'WebSocket即时通讯：断线自动重连(指数退避策略)、会话管理、未读消息角标(TabBar Badge)',
      '帖子社区功能：发帖/评论/回复、顶踩三态互斥、收藏、关注，全局事件总线同步状态',
      'Tabs+Swiper联动组件：手势跟随动画(线性插值)、Tab自动居中滚动、懒加载按需请求',
      '图片上传组件：多图选择(最多9张)、上传进度显示、上传中断(abort)、发布前校验'
    ]
  },
  {
    title: '超市订单管理系统',
    category: '企业管理系统',
    tagType: 'primary',
    images: supermarketImgList, 
    description: '基于 Spring Boot 3 + Vue3 的前后端分离企业级管理系统，聚焦供应链核心业务流程。采用 RBAC 权限模型实现细粒度访问控制，集成 JWT 无状态认证、Redis 缓存优化、AOP 统一日志等技术栈，独立完成从数据库设计到接口开发的全流程实现。系统支持用户管理、供应商管理、账单管理等模块，具备完整的 CRUD 操作、分页查询、数据校验及异常处理机制。',
    technologies: ['Spring Boot 3.1','MyBatis','MySQL 8.0','Redis','JWT','Vue 3','Element Plus','Vite'],
    features: [
      '设计并实现 RBAC 权限模型，支持菜单级与按钮级动态权限控制，通过自定义注解 + AOP 实现权限拦截',
      '基于 JWT Token 实现无状态认证机制，结合拦截器完成登录校验与 Token 刷新，保障会话安全',
      '引入 Redis 缓存验证码与热点数据，降低数据库压力，验证码有效期 5 分钟且单次有效',
      '使用 PageHelper 分页插件实现高效分页查询，支持多条件动态筛选与排序，优化大数据量场景性能',
      '采用 BCrypt 强哈希算法加密用户密码，防止明文存储风险，提升数据安全性',
      '通过 AOP 切面编程实现统一操作日志记录与声明式事务管理，确保业务数据一致性',
      '遵循 RESTful API 设计规范，统一响应格式与异常处理，提供清晰的接口文档',
      '前端采用 Vue3 Composition API + Element Plus 组件库，实现响应式布局与表单验证'
    ]
  }
])
</script>

<style lang="scss" scoped>
.home-page {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow-x: hidden;
  color: #303133;
}

/* 背景图片层 */
.background-layer {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  /* 使用一张柔和的抽象背景图 */
  background-image: url('https://images.unsplash.com/photo-1579546929518-9e396f3cc809?ixlib=rb-4.0.3&auto=format&fit=crop&w=2070&q=80');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  z-index: -2;
}

/* 遮罩层：让背景更柔和，不干扰文字阅读 */
.overlay-layer {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(255, 255, 255, 0.65);
  /* 白色半透明遮罩 */
  backdrop-filter: blur(8px);
  /* 轻微模糊背景图 */
  z-index: -1;
}

.site-header {
  background: rgba(255, 255, 255, 0.7);
  backdrop-filter: blur(12px);
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.03);
  padding: 0 40px;
  position: sticky;
  top: 0;
  z-index: 100;
  border-bottom: 1px solid rgba(255, 255, 255, 0.3);

  .header-content {
    display: flex;
    align-items: center;
    justify-content: space-between;
    max-width: 1200px;
    margin: 0 auto;
    width: 100%;
    height: 60px;
  }

  .site-title {
    font-size: 22px;
    font-weight: 700;
    background: linear-gradient(45deg, #409eff, #67c23a);
    -webkit-background-clip: text;
    background-clip: text;
    -webkit-text-fill-color: transparent;
    margin: 0;
  }
}

.site-main {
  flex: 1;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
  padding: 40px 20px;
}

.hero-section {
  padding: 80px 0;

  .hero-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 60px;
    max-width: 1000px;
    margin: 0 auto;
  }

  .hero-text {
    flex: 1;
    text-align: left;
    display: flex;
    flex-direction: column;
    gap: 24px;

    h2 {
      font-size: 52px;
      margin: 0;
      font-weight: 800;
      color: #2c3e50;
      line-height: 1.2;
      letter-spacing: -1px;
      text-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
    }

    p {
      font-size: 18px;
      color: #546e7a;
      margin: 0;
      line-height: 1.6;
    }

    .social-links {
      display: flex;
      gap: 12px;
      flex-wrap: wrap;
      margin-top: 10px;
    }
  }

  .hero-image-wrapper {
    flex-shrink: 0;

    .hero-avatar {
      border: 6px solid rgba(255, 255, 255, 0.9);
      box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
      transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
      border-radius: 40px;

      &:hover {
        transform: scale(1.05) rotate(3deg);
        box-shadow: 0 25px 50px rgba(0, 0, 0, 0.15);
      }
    }
  }
}

.glass-card {
  background: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(16px);
  border-radius: 24px;
  padding: 40px;
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.4);
  margin-bottom: 60px;
  transition: transform 0.3s ease;

  &:hover {
    transform: translateY(-5px);
    background: rgba(255, 255, 255, 0.75);
  }
}

.works-section {
  max-width: 1000px;
  margin: 0 auto;
}

.projects-container {
  margin-top: 40px;
}

.section-title {
  text-align: center;
  margin-bottom: 40px;

  h3 {
    font-size: 32px;
    margin: 0 0 8px;
    color: #2c3e50;
  }

  .section-subtitle {
    color: #7f8c8d;
    font-size: 14px;
    letter-spacing: 2px;
    text-transform: uppercase;
  }
}

.site-footer {
  text-align: center;
  padding: 30px 20px;
  color: #7f8c8d;
  font-size: 14px;
  background: rgba(255, 255, 255, 0.3);
}

/* AI 悬浮按钮容器 */
.ai-float-container {
  position: fixed;
  bottom: 40px;
  right: 40px;
  display: flex;
  align-items: center;
  gap: 12px;
  z-index: 999;
}

.ai-label {
  background: rgba(255, 255, 255, 0.9);
  padding: 6px 12px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 600;
  color: #409eff;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
  backdrop-filter: blur(4px);
  white-space: nowrap;
  border: 1px solid rgba(255, 255, 255, 0.5);
  animation: fadeInRight 0.5s ease-out;
}

.ai-float-trigger {
  width: 60px;
  height: 60px;
  background: linear-gradient(135deg, #409eff, #66b1ff);
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  box-shadow: 0 8px 20px rgba(64, 158, 255, 0.3);
  cursor: pointer;
  transition: all 0.3s ease;
  border: 2px solid rgba(255, 255, 255, 0.8);

  &:hover {
    transform: scale(1.1) translateY(-5px);
    box-shadow: 0 12px 28px rgba(64, 158, 255, 0.5);
  }
}

@keyframes fadeInRight {
  from {
    opacity: 0;
    transform: translateX(10px);
  }

  to {
    opacity: 1;
    transform: translateX(0);
  }
}

/* 聊天弹窗 */
.chat-popup {
  position: fixed;
  bottom: 110px;
  right: 40px;
  width: 380px;
  height: 550px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(20px);
  border-radius: 20px;
  box-shadow: 0 10px 40px rgba(0, 0, 0, 0.1);
  z-index: 998;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.6);

  .chat-header {
    height: 50px;
    background: rgba(245, 247, 250, 0.8);
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 20px;
    font-weight: 600;
    color: #303133;
    border-bottom: 1px solid rgba(0, 0, 0, 0.05);

    .close-btn {
      cursor: pointer;

      &:hover {
        color: #f56c6c;
      }
    }
  }
}

/* 动画 */
.chat-slide-enter-active,
.chat-slide-leave-active {
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.chat-slide-enter-from,
.chat-slide-leave-to {
  opacity: 0;
  transform: translateY(20px) scale(0.95);
}

@media (max-width: 768px) {
  .hero-container {
    flex-direction: column-reverse;
    text-align: center;
    gap: 40px;
  }

  .hero-text {
    align-items: center;
    text-align: center;

    h2 {
      font-size: 36px;
    }
  }

  .hero-image-wrapper .hero-avatar {
    :deep(.el-avatar) {
      width: 200px;
      height: 200px;
    }
  }
  
  .hero-section {
    padding: 40px 0;
  }
  
  .site-main {
    padding: 20px 15px;
  }
  
  .works-section {
    max-width: 100%;
  }
  
  .section-title {
    margin-bottom: 30px;
    
    h3 {
      font-size: 26px;
    }
  }

  .chat-popup {
    width: 90%;
    right: 5%;
    bottom: 100px;
    height: 60vh;
  }

  .ai-float-container {
    bottom: 20px;
    right: 20px;
    gap: 8px;
  }

  .ai-label {
    font-size: 12px;
    padding: 4px 8px;
  }

  .ai-float-trigger {
    width: 50px;
    height: 50px;

    .el-icon {
      font-size: 24px;
    }
  }
}
</style>