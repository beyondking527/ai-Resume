<template>
  <div class="image-carousel-wrapper">
    <el-carousel
      :interval="4000"
      height="400px"
      indicator-position="outside"
      arrow="always"
      type="card"
      @change="handleCarouselChange"
    >
      <el-carousel-item v-for="item in imageList" :key="item.id">
        <div class="carousel-item-content">
          <el-image
            :src="item.url"
            :preview-src-list="previewSrcList"
            :initial-index="item.id"
            fit="cover"
            class="carousel-image"
          />
          <div class="image-caption">
            <h3>{{ item.title }}</h3>
            <p>{{ item.description }}</p>
          </div>
        </div>
      </el-carousel-item>
    </el-carousel>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// 图片数据列表
const imageList = ref([
  {
    id: 0,
    url: '/images/work1.jpg',
    title: '作品一',
    description: '作品描述文字'
  },
  {
    id: 1,
    url: '/images/work2.jpg',
    title: '作品二',
    description: '作品描述文字'
  },
  {
    id: 2,
    url: '/images/work3.jpg',
    title: '作品三',
    description: '作品描述文字'
  }
])

// 预览图片列表（用于点击放大）
const previewSrcList = computed(() => imageList.value.map(item => item.url))

// 轮播切换事件
const handleCarouselChange = (newIndex, oldIndex) => {
  
}
</script>

<style lang="scss" scoped>
.image-carousel-wrapper {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.carousel-item-content {
  position: relative;
  width: 100%;
  height: 100%;
  border-radius: 12px;
  overflow: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);

  .carousel-image {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }

  .image-caption {
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    background: linear-gradient(to top, rgba(0, 0, 0, 0.7), transparent);
    color: #fff;
    padding: 40px 20px 20px;
    text-align: left;

    h3 {
      font-size: 20px;
      margin: 0 0 8px;
    }

    p {
      font-size: 14px;
      margin: 0;
      opacity: 0.9;
    }
  }
}

@media (max-width: 768px) {
  .image-carousel-wrapper {
    padding: 10px;
  }

  .carousel-item-content .image-caption {
    padding: 30px 15px 15px;

    h3 {
      font-size: 16px;
    }

    p {
      font-size: 12px;
    }
  }
}
</style>