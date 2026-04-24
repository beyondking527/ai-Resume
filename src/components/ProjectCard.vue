<template>
  <div class="project-card glass-card">
    <div class="project-header">
      <h3 class="project-title">{{ project.title }}</h3>
      <el-tag :type="project.tagType" effect="dark" round>{{ project.category }}</el-tag>
    </div>
    
    <div class="project-carousel">
      <ImageCarousel
        v-if="project.images && project.images.length > 0"
        :images="formattedImages"
        height="350px"
        :interval="5000"
        :show-indicators="project.images.length > 1"
        :show-arrows="project.images.length > 1"
        indicator-position="outside"
        arrow-type="hover"
        image-fit="contain"
        background-color="#f5f7fa"
        @change="handleImageChange"
      />
    </div>

    <div class="project-info">
      <div class="info-section">
        <h4 class="section-label">项目简介</h4>
        <p class="project-description">{{ project.description }}</p>
      </div>

      <div class="info-section">
        <h4 class="section-label">核心技术栈</h4>
        <div class="tech-stack">
          <el-tag
            v-for="(tech, index) in project.technologies"
            :key="index"
            size="small"
            effect="plain"
            class="tech-tag"
          >
            {{ tech }}
          </el-tag>
        </div>
      </div>

      <div class="info-section" v-if="project.features && project.features.length">
        <h4 class="section-label">功能亮点</h4>
        <ul class="feature-list">
          <li v-for="(feature, index) in project.features" :key="index">
            <el-icon><Check /></el-icon>
            <span>{{ feature }}</span>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { Check } from '@element-plus/icons-vue'
import ImageCarousel from '@/components/ImageCarousel.vue'

const props = defineProps({
  project: {
    type: Object,
    required: true
  }
})

// 将图片数组转换为 ImageCarousel 需要的格式
const formattedImages = computed(() => {
  if (!props.project.images || props.project.images.length === 0) {
    return []
  }
  
  // 如果已经是对象格式，直接返回
  if (typeof props.project.images[0] === 'object') {
    return props.project.images
  }
  
  // 如果是字符串数组，转换为对象格式
  return props.project.images.map((img, index) => ({
    url: img,
    alt: `${props.project.title} - 图片 ${index + 1}`,
    title: index === 0 ? props.project.title : undefined
  }))
})

// 处理图片切换事件
const handleImageChange = (index, image) => {
  console.log(`切换到第 ${index + 1} 张图片:`, image)
}
</script>

<style lang="scss" scoped>
.project-card {
  margin-bottom: 60px;
  padding: 40px;
  background: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(16px);
  border-radius: 24px;
  box-shadow: 0 8px 32px rgba(31, 38, 135, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.4);
  transition: all 0.3s ease;

  &:hover {
    transform: translateY(-5px);
    background: rgba(255, 255, 255, 0.75);
    box-shadow: 0 12px 40px rgba(31, 38, 135, 0.1);
  }
}

.project-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 30px;

  .project-title {
    font-size: 28px;
    font-weight: 700;
    color: #2c3e50;
    margin: 0;
  }
}

.project-carousel {
  margin-bottom: 30px;
  border-radius: 16px;
  overflow: hidden;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);

  :deep(.image-carousel) {
    border-radius: 16px;
  }

  :deep(.el-carousel__indicators--outside) {
    bottom: -30px;
  }
}

.project-info {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.info-section {
  .section-label {
    font-size: 16px;
    font-weight: 600;
    color: #409eff;
    margin: 0 0 12px 0;
    display: flex;
    align-items: center;
    gap: 8px;

    &::before {
      content: '';
      display: inline-block;
      width: 4px;
      height: 16px;
      background: linear-gradient(180deg, #409eff, #67c23a);
      border-radius: 2px;
    }
  }

  .project-description {
    font-size: 15px;
    line-height: 1.8;
    color: #606266;
    margin: 0;
    text-align: justify;
  }

  .tech-stack {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;

    .tech-tag {
      font-size: 13px;
      padding: 6px 14px;
      border-radius: 20px;
      transition: all 0.3s ease;

      &:hover {
        transform: translateY(-2px);
        box-shadow: 0 4px 8px rgba(64, 158, 255, 0.2);
      }
    }
  }

  .feature-list {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
    flex-direction: column;
    gap: 10px;

    li {
      display: flex;
      align-items: flex-start;
      gap: 10px;
      font-size: 14px;
      color: #606266;
      line-height: 1.6;

      .el-icon {
        color: #67c23a;
        margin-top: 2px;
        flex-shrink: 0;
      }
    }
  }
}

@media (max-width: 768px) {
  .project-card {
    padding: 24px;
    margin-bottom: 40px;
  }

  .project-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 12px;

    .project-title {
      font-size: 22px;
    }
  }

  .project-carousel {
    :deep(.image-carousel) {
      height: 250px !important;
    }
  }

  .info-section {
    .section-label {
      font-size: 15px;
    }

    .project-description {
      font-size: 14px;
    }
  }
}
</style>