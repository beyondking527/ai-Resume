<template>
  <div class="image-carousel" :style="{ height: carouselHeight }">
    <el-carousel
      :interval="interval"
      :height="carouselHeight"
      :indicator-position="showIndicators ? indicatorPosition : 'none'"
      :arrow="showArrows ? arrowType : 'never'"
      :loop="loop"
      :autoplay="autoplay"
      trigger="click"
      @change="handleCarouselChange"
    >
      <el-carousel-item v-for="(img, index) in images" :key="index">
        <div class="image-wrapper" @click="openPreview(index)">
          <el-image
            :src="img.url || img"
            :alt="img.alt || `图片 ${index + 1}`"
            fit="contain"
            class="carousel-image"
            lazy
            @load="handleImageLoad(index)"
            @error="handleImageError(index)"
          >
            <template #error>
              <div class="image-error">
                <el-icon><Picture /></el-icon>
                <span>加载失败</span>
              </div>
            </template>
            <template #placeholder>
              <div class="image-loading">
                <el-icon class="is-loading"><Loading /></el-icon>
                <span>加载中...</span>
              </div>
            </template>
          </el-image>
          
          <!-- 图片标题/描述 -->
          <div v-if="img.title || img.description" class="image-caption">
            <h4 v-if="img.title" class="caption-title">{{ img.title }}</h4>
            <p v-if="img.description" class="caption-desc">{{ img.description }}</p>
          </div>
        </div>
      </el-carousel-item>
    </el-carousel>
  </div>

  <!-- 全屏预览 - 使用 Teleport 传送到 body -->
  <Teleport to="body">
    <transition name="preview-fade">
      <div v-if="previewVisible" class="image-preview-overlay" @click.self="closePreview">
        <button class="preview-close-btn" @click="closePreview">
          <el-icon><Close /></el-icon>
        </button>
        <div class="preview-container">
          <img 
            :src="getImageUrl(currentPreviewIndex)" 
            :alt="getImageAlt(currentPreviewIndex)"
            class="preview-image"
          />
        </div>
      </div>
    </transition>
  </Teleport>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import { Picture, Loading, Close } from '@element-plus/icons-vue'

const props = defineProps({
  // 图片列表，支持字符串数组或对象数组
  images: {
    type: Array,
    required: true,
    default: () => []
  },
  // 轮播高度（支持固定值或响应式）
  height: {
    type: [String, Number],
    default: '400px'
  },
  // 是否保持宽高比
  maintainAspectRatio: {
    type: Boolean,
    default: false
  },
  // 宽高比（当 maintainAspectRatio 为 true 时生效）
  aspectRatio: {
    type: Number,
    default: 16 / 9
  },
  // 自动播放间隔（毫秒）
  interval: {
    type: Number,
    default: 5000
  },
  // 是否循环播放
  loop: {
    type: Boolean,
    default: true
  },
  // 是否自动播放
  autoplay: {
    type: Boolean,
    default: true
  },
  // 是否显示指示器
  showIndicators: {
    type: Boolean,
    default: true
  },
  // 指示器位置
  indicatorPosition: {
    type: String,
    default: 'outside',
    validator: (value) => ['outside', 'inside', 'none'].includes(value)
  },
  // 是否显示箭头
  showArrows: {
    type: Boolean,
    default: true
  },
  // 箭头显示类型
  arrowType: {
    type: String,
    default: 'hover',
    validator: (value) => ['always', 'hover', 'never'].includes(value)
  },
  // 图片适应模式
  imageFit: {
    type: String,
    default: 'contain',
    validator: (value) => ['fill', 'contain', 'cover', 'none', 'scale-down'].includes(value)
  },
  // 背景色
  backgroundColor: {
    type: String,
    default: '#f5f7fa'
  }
})

const emit = defineEmits(['change', 'load', 'error'])

const currentIndex = ref(0)
const previewVisible = ref(false)
const currentPreviewIndex = ref(0)

// 计算轮播高度
const carouselHeight = computed(() => {
  if (props.maintainAspectRatio) {
    return 'auto'
  }
  return typeof props.height === 'number' ? `${props.height}px` : props.height
})

// 处理轮播切换
const handleCarouselChange = (index) => {
  currentIndex.value = index
  emit('change', index, props.images[index])
}

// 处理图片加载成功
const handleImageLoad = (index) => {
  emit('load', index, props.images[index])
}

// 处理图片加载失败
const handleImageError = (index) => {
  emit('error', index, props.images[index])
  console.warn(`图片加载失败:`, props.images[index])
}

// 获取图片URL
const getImageUrl = (index) => {
  const img = props.images[index]
  return img.url || img
}

// 获取图片alt文本
const getImageAlt = (index) => {
  const img = props.images[index]
  return img.alt || `图片 ${index + 1}`
}

// 打开预览
const openPreview = (index) => {
  currentPreviewIndex.value = index
  previewVisible.value = true
  document.body.style.overflow = 'hidden'
}

// 关闭预览
const closePreview = () => {
  previewVisible.value = false
  document.body.style.overflow = ''
}

// 监听ESC键关闭预览
watch(previewVisible, (visible) => {
  if (visible) {
    const handleEsc = (e) => {
      if (e.key === 'Escape') {
        closePreview()
      }
    }
    window.addEventListener('keydown', handleEsc)
    return () => window.removeEventListener('keydown', handleEsc)
  }
})

// 暴露方法供父组件调用
defineExpose({
  currentIndex,
  openPreview
})
</script>

<style scoped>
.image-carousel {
  position: relative;
  width: 100%;
  border-radius: 8px;
  overflow: hidden;
  background-color: v-bind(backgroundColor);
}

.image-wrapper {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.carousel-image {
  width: 100%;
  height: 100%;
  object-fit: v-bind(imageFit);
}

/* 图片标题/描述 */
.image-caption {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  padding: 20px;
  background: linear-gradient(to top, rgba(0, 0, 0, 0.7), transparent);
  color: white;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.image-wrapper:hover .image-caption {
  opacity: 1;
}

.caption-title {
  margin: 0 0 8px 0;
  font-size: 16px;
  font-weight: 600;
}

.caption-desc {
  margin: 0;
  font-size: 14px;
  opacity: 0.9;
}

/* 加载状态 */
.image-loading,
.image-error {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  color: #909399;
  gap: 8px;
}

.image-error {
  color: #f56c6c;
}

/* Element Plus 轮播样式覆盖 */
:deep(.el-carousel__container) {
  height: 100%;
}

:deep(.el-carousel__item) {
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  /* 移除可能导致空白的默认边距 */
  margin: 0;
  padding: 0;
}

:deep(.el-image__inner) {
  max-width: 100%;
  max-height: 100%;
  /* 确保图片没有额外的垂直空白 */
  vertical-align: top;
  display: block;
}

/* 全屏预览样式 - 由于使用 Teleport，需要使用全局样式 */
.image-preview-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.9);
  z-index: 9999;
  display: flex;
  align-items: center;
  justify-content: center;
}

/* 确保关闭按钮相对于 overlay 定位 */
.image-preview-overlay .preview-close-btn {
  position: absolute;
  top: 20px;
  right: 20px;
  width: 48px;
  height: 48px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.2);
  border: none;
  color: white;
  font-size: 24px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  z-index: 10000;
}

.image-preview-overlay .preview-close-btn:hover {
  background-color: rgba(255, 255, 255, 0.3);
  transform: scale(1.1);
}

.image-preview-overlay .preview-container {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

.image-preview-overlay .preview-image {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  display: block;
}

/* 桌面端优化 - 限制最大显示尺寸 */
@media (min-width: 1025px) {
  .image-preview-overlay .preview-image {
    max-width: 90vw;
    max-height: 90vh;
  }
}

/* 平板端优化 */
@media (max-width: 1024px) and (min-width: 769px) {
  .image-preview-overlay .preview-image {
    max-width: 85vw;
    max-height: 85vh;
  }
}

/* 手机端优化 */
@media (max-width: 768px) {
  .image-preview-overlay .preview-image {
    max-width: 95vw;
    max-height: 80vh;
  }
  
  .image-preview-overlay .preview-close-btn {
    top: 10px;
    right: 10px;
    width: 44px;
    height: 44px;
    font-size: 22px;
    /* 增大触摸区域，更适合手指操作 */
    -webkit-tap-highlight-color: transparent;
  }
  
  .image-preview-overlay .preview-close-btn:active {
    background-color: rgba(255, 255, 255, 0.4);
    transform: scale(0.95);
  }
  
  /* 修复手机端轮播图片底部空白问题 */
  .image-carousel {
    /* 确保容器没有额外的底部空间 */
    line-height: 0;
  }
  
  :deep(.el-carousel__item) {
    /* 确保轮播项没有额外的垂直空间 */
    line-height: 0;
  }
  
  .image-wrapper {
    /* 确保图片包装器紧凑 */
    line-height: 0;
  }
  
  .carousel-image {
    /* 确保图片紧密贴合容器 */
    line-height: 0;
    vertical-align: top;
  }
  
  :deep(.el-image) {
    /* 覆盖 Element Plus 图片组件的默认样式 */
    line-height: 0;
    display: block;
  }
  
  :deep(.el-image__inner) {
    /* 确保图片完全填充且无空白 */
    vertical-align: top;
    display: block;
  }
}

/* 超小屏幕手机优化 */
@media (max-width: 480px) {
  .image-preview-overlay .preview-image {
    max-width: 98vw;
    max-height: 75vh;
  }
  
  .image-preview-overlay .preview-close-btn {
    top: 8px;
    right: 8px;
    width: 36px;
    height: 36px;
    font-size: 18px;
  }
}

/* 过渡动画 */
.preview-fade-enter-active,
.preview-fade-leave-active {
  transition: opacity 0.3s ease;
}

.preview-fade-enter-from,
.preview-fade-leave-to {
  opacity: 0;
}

.preview-fade-enter-active .preview-image,
.preview-fade-leave-active .preview-image {
  transition: transform 0.3s ease;
}

.preview-fade-enter-from .preview-image,
.preview-fade-leave-to .preview-image {
  transform: scale(0.9);
}
</style>
