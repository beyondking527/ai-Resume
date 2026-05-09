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
        
        <!-- 上一张按钮 -->
        <button 
          v-if="images.length > 1" 
          class="preview-nav-btn preview-prev-btn" 
          @click.stop="prevImage"
        >
          <el-icon><ArrowLeft /></el-icon>
        </button>
        
        <!-- 下一张按钮 -->
        <button 
          v-if="images.length > 1" 
          class="preview-nav-btn preview-next-btn" 
          @click.stop="nextImage"
        >
          <el-icon><ArrowRight /></el-icon>
        </button>
        
        <!-- 图片计数器 -->
        <div v-if="images.length > 1" class="preview-counter">
          {{ currentPreviewIndex + 1 }} / {{ images.length }}
        </div>
        
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
import { Picture, Loading, Close, ArrowLeft, ArrowRight } from '@element-plus/icons-vue'

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

// 上一张图片
const prevImage = () => {
  if (currentPreviewIndex.value > 0) {
    currentPreviewIndex.value--
  } else {
    // 循环到最后一张
    currentPreviewIndex.value = props.images.length - 1
  }
}

// 下一张图片
const nextImage = () => {
  if (currentPreviewIndex.value < props.images.length - 1) {
    currentPreviewIndex.value++
  } else {
    // 循环到第一张
    currentPreviewIndex.value = 0
  }
}

// 监听键盘事件
watch(previewVisible, (visible) => {
  if (visible) {
    const handleKeyDown = (e) => {
      if (e.key === 'Escape') {
        closePreview()
      } else if (e.key === 'ArrowLeft') {
        prevImage()
      } else if (e.key === 'ArrowRight') {
        nextImage()
      }
    }
    window.addEventListener('keydown', handleKeyDown)
    return () => window.removeEventListener('keydown', handleKeyDown)
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
  background-color: v-bind(backgroundColor);
}

:deep(.el-image) {
  width: 100%;
  height: 100%;
}

:deep(.el-image__inner) {
  width: 100%;
  height: 100%;
  object-fit: v-bind(imageFit);
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

/* 预览切换按钮样式 */
.image-preview-overlay .preview-nav-btn {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  width: 50px;
  height: 50px;
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
  backdrop-filter: blur(4px);
}

.image-preview-overlay .preview-nav-btn:hover {
  background-color: rgba(255, 255, 255, 0.35);
  transform: translateY(-50%) scale(1.1);
}

.image-preview-overlay .preview-nav-btn:active {
  transform: translateY(-50%) scale(0.95);
}

.image-preview-overlay .preview-prev-btn {
  left: 20px;
}

.image-preview-overlay .preview-next-btn {
  right: 20px;
}

/* 图片计数器 */
.image-preview-overlay .preview-counter {
  position: absolute;
  bottom: 30px;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  padding: 8px 16px;
  border-radius: 20px;
  font-size: 14px;
  font-weight: 500;
  z-index: 10000;
  backdrop-filter: blur(4px);
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
  
  /* 手机端切换按钮优化 */
  .image-preview-overlay .preview-nav-btn {
    width: 44px;
    height: 44px;
    font-size: 20px;
    -webkit-tap-highlight-color: transparent;
  }
  
  .image-preview-overlay .preview-prev-btn {
    left: 10px;
  }
  
  .image-preview-overlay .preview-next-btn {
    right: 10px;
  }
  
  .image-preview-overlay .preview-nav-btn:active {
    background-color: rgba(255, 255, 255, 0.4);
    transform: translateY(-50%) scale(0.95);
  }
  
  /* 手机端计数器优化 */
  .image-preview-overlay .preview-counter {
    bottom: 20px;
    font-size: 13px;
    padding: 6px 14px;
  }
  
  /* 手机端轮播容器优化 */
  :deep(.el-carousel__item) {
    display: block;
  }
  
  :deep(.el-image__inner) {
    width: 100%;
    height: auto;
    min-height: 100%;
    object-fit: cover;
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
  
  /* 超小屏幕切换按钮优化 */
  .image-preview-overlay .preview-nav-btn {
    width: 38px;
    height: 38px;
    font-size: 16px;
  }
  
  .image-preview-overlay .preview-prev-btn {
    left: 8px;
  }
  
  .image-preview-overlay .preview-next-btn {
    right: 8px;
  }
  
  .image-preview-overlay .preview-counter {
    bottom: 15px;
    font-size: 12px;
    padding: 5px 12px;
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