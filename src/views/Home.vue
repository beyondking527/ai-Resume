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
    title: "熊猫商城管理后台",
    category: "电商管理平台",
    tagType: "primary",
    images: shopImgList,
    description: "基于 Spring Boot 2.7 + Vue 3 开发的现代化商城管理后台系统，采用前后端分离架构。系统实现了完整的 RBAC 权限管理、多级商品分类、SPU/SKU 多维度管理、订单状态机、二级分销体系等核心业务模块。集成 JWT 无状态认证、BCrypt 加密、Redis 缓存、OSS 对象存储，并提供接口文档、全局异常处理、统一响应格式等企业级特性。",
    technologies: ["Spring Boot 2.7", "MyBatis-Plus 3.5", "MySQL 8.0", "Redis", "Vue 3", "Element Plus", "JWT"],
    features: [
      "SKU/SPU商品管理：支持全局规格和商品专属规格，多规格商品通过规格值笛卡尔积算法自动生成SKU组合，并在事务中保证商品与SKU数据的一致性。",
      "订单状态机：管理从待付款到完成的完整生命周期，在确认收货和同意退款时，通过 @Transactional 事务保证订单状态、库存扣减/回滚、商品销量更新（及SKU销量）的强一致性。",
      "二级分销体系：通过邀请码绑定上下级关系，在订单完成后按配置比例自动计算一/二级佣金，并利用 Redis 实现分布式锁，确保高并发下佣金计算的准确性和唯一性。",
      "认证与安全：采用 JWT 无状态认证，结合 BCrypt 加密存储密码。并利用 Redis 记录登录失败次数，实现账号锁定机制，有效防止暴力破解。",
      "前端深度封装：Axios 请求拦截器统一注入 Token 并处理 401 自动跳转登录；Vue Router 动态路由根据后端菜单权限生成；Element Plus 二次封装与字典组件简化开发。",
      "数据库设计优化：针对订单、商品、用户等核心业务表设计了合理的索引（如联合索引 idx_status_created），并利用逻辑删除（软删除）保留数据，提升查询效率和可恢复性。"
    ]
  },
  {
    title: "面霸AI - 智能面试模拟助手",
    category: "AI面试训练平台",
    tagType: "success",
    images: AIinterviewImgList,
    description: "基于 RAG（检索增强生成）技术构建的智能面试训练系统，完全部署于 Google Colab 免费 GPU 环境，使用本地开源模型 Qwen2.5-7B（4-bit 量化）和 BGE-M3 嵌入模型，脱离第三方 API 依赖。用户可上传自定义面试题库（JSON/Markdown/PDF/Word/Excel/TXT），系统通过向量检索召回相关题目，生成结构化参考答案或对用户回答进行点评。支持“面试教练”和“答案点评”两种模式，提供流式对话、对话历史管理、随机出题、数据库管理等功能，并通过 Gradio share=True 生成公网链接，实现零成本云端访问。",
    technologies: ["Python", "Gradio", "ChromaDB", "Ollama", "Qwen2.5-7B", "BGE-M3", "LangChain", "PDF/Word/Excel解析", "RAG", "Google Colab"],
    features: [
        "多格式文档解析与向量化入库：支持 PDF、DOCX、XLSX、TXT、Markdown、JSON 六种格式，自动提取题目与答案，通过 BGE-M3 嵌入模型向量化后存入 ChromaDB 本地向量库。",
        "双模式对话：面试教练模式下，基于 RAG 检索相似题目生成五段式参考答案；答案点评模式下随机出题，用户回答后 AI 进行 10 分制评分与改进建议。",
        "相似度阈值过滤与重排：召回后根据余弦距离阈值过滤低相关文档，保留 Top‑K 条作为参考资料，有效提升回答质量。",
        "对话历史管理：自动截断历史（保留最近 10 轮对话），保证大模型能理解上下文，同时避免超出模型上下文窗口；支持清空对话、随机出题。",
        "流式输出与 Web 界面：使用 Gradio 构建前后端，对接 Ollama 流式生成 API，实现打字机效果；支持 share=True 生成公网链接，可外网访问。",
        "零成本云部署：项目完整迁移至 Google Colab，利用免费 Tesla T4 GPU 运行 Qwen2.5-7B 量化模型；通过挂载 Google Drive 实现代码、向量数据库、模型文件的持久化，并提供一键恢复脚本，彻底解决会话丢失问题。",
        "本地模型替代：使用 Ollama 部署 Qwen2.5-7B（4-bit 量化）对话模型和 BGE-M3 嵌入模型，完全替代智谱 API，保证离线运行、数据隐私、无调用费用。",
        "集合中文名友好显示：ChromaDB 集合名只支持字母数字下划线，通过 MD5 哈希映射并存储原始中文名到 metadata，UI 下拉菜单显示中文名称。"
    ]
  },
  {
    title: "亲喝水智能售货机管理系统",
    category: "智慧零售",
    tagType: "primary",
    images: vendingImgList,
    description: "基于若依(RuoYi)框架二次开发的智能售货机运营管理系统，采用多端架构（管理后台Web + 运维人员App + 用户小程序），实现设备全生命周期管理、工单运维、商品销售、点位合作商管理、支付集成等完整业务。后端使用 Spring Boot + MyBatis + Redis + JWT，前端使用 Vue 3 + Element Plus，App端和小程序端独立部署，共享数据库。",
    technologies: ["Spring Boot", "MyBatis", "MySQL", "Redis", "JWT", "Spring Security", "PageHelper", "AOP", "Vue 3", "Element Plus", "Vite", "若依框架", "MyBatis-Plus", "Hutool", "ElegentPay", "阿里云OSS"],
    features: [
        "多端协同架构：管理后台、运维App、用户小程序三端独立后端服务，共用 MySQL + Redis，设备状态、工单数据实时同步。",
        "设备全生命周期管理：设备从创建设型号、绑定点位、投放、运营、补货、维修到撤机，全流程工单驱动，状态机自动流转。",
        "工单状态机与自动业务操作：投放/撤机工单完成后自动更新设备状态；补货工单完成后自动增加对应货道库存。",
        "RBAC权限模型 + 数据权限：用户→角色→菜单/按钮，结合 @DataScope AOP 实现部门级数据隔离。",
        "若依框架特性集成：@Log 操作日志、@RepeatSubmit 防重复提交、@RateLimiter 接口限流（Redis+Lua）、XSS防护、代码生成。",
        "App端轻量认证：使用 MyBatis-Plus + Hutool + 自定义 JWT，独立于管理后台，面向运维人员。",
        "小程序端支付集成：集成 ElegentPay SDK，支持微信支付，订单与设备出货联动。",
        "文件存储统一抽象：使用 x-file-storage 组件，支持阿里云OSS，配置灵活。"
    ]
  },
  {
    title: "陌域社区交友",
    category: "移动社交",
    tagType: "primary",
    images: communityImgList,
    description: "基于 uni-app x 框架开发的社区交友移动应用，使用 UTS（Uni TypeScript）强类型语言，编译为原生应用（Android Kotlin / iOS Swift），实现原生级渲染性能。应用包含帖子发布/浏览、评论互动、即时聊天（WebSocket）、用户关注、话题分类等核心社交功能，采用事件驱动架构和全局状态管理，支持多端（App）部署。",
    technologies: ["uni-app x", "UTS", "Vue 3", "WebSocket", "uni.request", "原生渲染"],
    features: [
        "强类型系统：通过 type.uts 定义 20+ 数据模型，涵盖帖子、评论、用户、会话、消息等所有业务实体，API 响应使用泛型 Result<T> 实现类型安全。",
        "WebSocket 即时通讯：实现完整生命周期管理（连接、自动重连、心跳检测、消息分发），支持单聊、未读角标、消息已读标记。",
        "事件驱动架构：使用 uni.$on/emit/off 实现组件间松耦合通信，包括帖子变更、评论成功、会话更新、未读数变化等 10+ 全局事件。",
        "动态 Tab 与懒加载：首页根据 API 动态加载帖子分类，每个分类对应一个 tabs-swiper 项，首次可见时才触发数据加载，减少首屏请求。",
        "顶踩三态切换：支持顶/踩/取消三种状态，通过全局事件实时同步列表页和详情页的数据状态。",
        "长列表统一封装：long-list-page 组件封装帖子、话题、用户、评论四种列表类型的分页加载与实时刷新逻辑。",
        "登录拦截与状态管理：store/user.uts 集中管理用户登录状态、信息缓存、Token 持久化，提供 AuthNavigateTo / AuthAction 统一登录拦截。"
    ]
  },
  {
    title: "超市账单管理系统",
    category: "企业管理后台",
    tagType: "primary",
    images: supermarketImgList,
    description: "基于 Spring Boot 3 + Vue 3 前后端分离架构的超市账单（订单）管理系统，实现用户管理、供应商管理、账单管理、权限菜单管理等核心业务功能。系统采用 UUID Token + Redis 认证方案，支持 RBAC 权限模型，具备登录/登出/忘记密码/修改密码等完整安全功能，通过 AOP + 策略模式实现差异化日志记录。",
    technologies: ["Spring Boot 3", "MyBatis", "MySQL", "Redis", "JWT (jjwt)", "BCrypt", "PageHelper", "AOP", "Vue 3", "Element Plus", "Axios", "Vite"],
    features: [
        "UUID Token + Redis 认证方案：Token 以 UUID 形式存储于 Redis，支持主动失效和自动续期，相比自包含 JWT 可随时踢人下线。",
        "RBAC 权限模型：基于角色控制权限，权限表支持树形结构（菜单/按钮/接口），通过 role_permission 关联表灵活分配。",
        "策略模式 AOP 日志切面：根据 Service 层方法操作的不同实体（用户/账单/供应商），自动选择对应日志策略，记录操作前后及异常信息。",
        "统一错误码体系：错误码分段管理（通用/认证/用户/业务），前端可根据错误码差异化处理，如 2002/2003 自动跳转登录。",
        "分页双方案：支持 PageHelper 插件分页和手写 PageSupport 分页两种方式，适应不同场景。",
        "前端 Axios 封装：请求拦截器自动附加 JWT Token，响应拦截器统一处理错误码，401/2002/2003 自动清除 Token 并跳转登录。",
        "Vue Router 导航守卫：基于路由元信息（requiresAuth / public）控制页面访问权限，未登录自动重定向并携带 redirect 参数。"
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