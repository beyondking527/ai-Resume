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
        <div class="image-wrapper" :class="{ 'is-active': currentIndex === index }">
          <el-image
            :src="img.url || img"
            :alt="img.alt || `图片 ${index + 1}`"
            fit="contain"
            class="carousel-image"
            :preview-src-list="getImageList()"
            :initial-index="index"
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

    <!-- 自定义指示器（可选） -->
    <div v-if="showCustomIndicators && images.length > 1" class="custom-indicators">
      <span 
        v-for="(img, index) in images" 
        :key="index"
        class="indicator-dot"
        :class="{ active: currentIndex === index }"
        @click="goToSlide(index)"
      ></span>
    </div>

    <!-- 图片计数 -->
    <div v-if="showCounter && images.length > 1" class="image-counter">
      {{ currentIndex + 1 }} / {{ images.length }}
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { Picture, Loading } from '@element-plus/icons-vue'

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
  // 是否显示自定义指示器
  showCustomIndicators: {
    type: Boolean,
    default: false
  },
  // 是否显示计数器
  showCounter: {
    type: Boolean,
    default: false
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
const loadedImages = ref(new Set())
const failedImages = ref(new Set())

// 计算轮播高度
const carouselHeight = computed(() => {
  if (props.maintainAspectRatio) {
    return 'auto'
  }
  return typeof props.height === 'number' ? `${props.height}px` : props.height
})

// 获取图片列表用于预览
const getImageList = () => {
  return props.images.map(img => img.url || img)
}

// 处理轮播切换
const handleCarouselChange = (index) => {
  currentIndex.value = index
  emit('change', index, props.images[index])
}

// 跳转到指定幻灯片
const goToSlide = (index) => {
  currentIndex.value = index
}

// 处理图片加载成功
const handleImageLoad = (index) => {
  loadedImages.value.add(index)
  emit('load', index, props.images[index])
}

// 处理图片加载失败
const handleImageError = (index) => {
  failedImages.value.add(index)
  emit('error', index, props.images[index])
  console.warn(`图片加载失败:`, props.images[index])
}

// 暴露方法供父组件调用
defineExpose({
  currentIndex,
  loadedImages,
  goToSlide
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
  transition: all 0.3s ease;
}

.carousel-image {
  width: 100%;
  height: 100%;
  object-fit: v-bind(imageFit);
  transition: transform 0.3s ease;
}

.image-wrapper:hover .carousel-image {
  transform: scale(1.02);
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

/* 自定义指示器 */
.custom-indicators {
  position: absolute;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 8px;
  z-index: 10;
}

.indicator-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background-color: rgba(255, 255, 255, 0.5);
  cursor: pointer;
  transition: all 0.3s ease;
}

.indicator-dot:hover {
  background-color: rgba(255, 255, 255, 0.8);
  transform: scale(1.2);
}

.indicator-dot.active {
  background-color: #fff;
  width: 24px;
  border-radius: 4px;
}

/* 图片计数器 */
.image-counter {
  position: absolute;
  top: 15px;
  right: 15px;
  padding: 4px 12px;
  background-color: rgba(0, 0, 0, 0.6);
  color: white;
  border-radius: 12px;
  font-size: 12px;
  z-index: 10;
}

/* 响应式设计 */
@media (max-width: 768px) {
  .image-caption {
    padding: 12px;
  }
  
  .caption-title {
    font-size: 14px;
  }
  
  .caption-desc {
    font-size: 12px;
  }
  
  .custom-indicators {
    bottom: 10px;
  }
  
  .indicator-dot {
    width: 6px;
    height: 6px;
  }
  
  .indicator-dot.active {
    width: 18px;
  }
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
}

:deep(.el-image__inner) {
  max-width: 100%;
  max-height: 100%;
}
</style>