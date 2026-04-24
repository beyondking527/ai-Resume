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

import shopImg1 from '@/assets/imagesProject/shopadmin/商城后台管理-权限与角色.png'
import shopImg2 from '@/assets/imagesProject/shopadmin/商城后台管理-商品分类管理.png'
import shopImg3 from '@/assets/imagesProject/shopadmin/商城后台管理-商品管理.png'
import shopImg4 from '@/assets/imagesProject/shopadmin/商城后台管理-图库模块.png'
import shopImg5 from '@/assets/imagesProject/shopadmin/商城后台管理-主控台.png'
import shopImg6 from '@/assets/imagesProject/shopadmin/商品后台管理-订单与物流.png'

const isChatVisible = ref(false)

const toggleChat = () => {
  isChatVisible.value = !isChatVisible.value
}

const projects = ref([
  {
    title: '商城管理后台',
    category: '电商管理平台',
    tagType: 'primary',
    images: [shopImg1, shopImg2, shopImg3, shopImg4, shopImg5, shopImg6], // 多张图片数组
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
    title: '帝可得自动贩卖机管理平台',
    category: 'IoT 物联网',
    tagType: 'success',
    images: [vendingImg1, vendingImg2], 
    description: '面向智能零售场景的 IoT 设备管理平台，通过 MQTT 协议实现与数千台自动贩卖机的实时通信。平台提供设备监控、库存预警、远程运维等智能化功能，助力新零售业务数字化转型。',
    technologies: ['SpringCloud', 'Nacos', 'OpenFeign', 'MQTT', '华为云 IoT', 'RabbitMQ', 'Docker'],
    features: [
      '基于 MQTT 协议实现设备实时状态上报与指令下发',
      '集成华为云 IoT 平台，支持海量设备接入与管理',
      '智能库存预警系统，自动生成补货建议',
      '可视化设备监控大屏，实时展示运营数据'
    ]
  },
  {
    title: '社区交友 App',
    category: '社交应用',
    tagType: 'warning',
    images: [communityImg1, communityImg2], 
    description: '基于地理位置的社区社交平台，为用户提供邻里互动、兴趣小组、活动发布等功能。采用微服务架构设计，支持高并发用户访问，注重用户隐私保护与内容安全审核。',
    technologies: ['SpringBoot', 'MySQL', 'Redis', '阿里云 OSS', 'WebSocket', 'Vue3', 'UniApp'],
    features: [
      'LBS 地理位置服务，精准推荐附近用户与活动',
      '集成阿里云 OSS 实现图片/视频高效存储与 CDN 加速',
      'WebSocket 实时消息推送，支持私聊与群聊功能',
      '敏感词过滤与内容审核机制，保障社区健康环境'
    ]
  },
  {
    title: '超市订单管理系统',
    category: '企业管理',
    tagType: 'danger',
    images: [supermarketImg1, supermarketImg2], 
    description: '面向连锁超市的智能化订单管理平台，涵盖采购订单、销售订单、库存调拨等全流程业务。系统采用分布式架构设计，支持多门店数据同步与实时库存管理，有效提升供应链效率。',
    technologies: ['SpringCloud', 'MyBatis', 'MySQL', 'Redis', 'RabbitMQ', 'Linux', 'Nginx'],
    features: [
      '多租户架构设计，支持连锁门店独立数据隔离',
      '基于 RabbitMQ 的异步订单处理，峰值吞吐量提升 3 倍',
      '智能库存预警与自动补货建议，降低缺货率 40%',
      '多维度数据统计报表，支持 Excel/PDF 格式导出'
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

  .chat-popup {
    width: 90%;
    right: 5%;
    bottom: 100px;
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