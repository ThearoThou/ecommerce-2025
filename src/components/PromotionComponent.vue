  <template>
  <div class="promotion-card" :style="{ backgroundColor: bgColor }">
    <div class="text">
      <h3 :style="{ color: titleColor }">{{ title }}</h3>
      <p>{{ description }}</p>
      <slot></slot>
    </div>

    <div class="image-container" :style="{ backgroundColor: imageBackgroundColor }">
      <img
        :src="banner"
        alt="promotion banner"
        :style="{ width: imageWidth, height: imageHeight }"
        @load="extractColorFromImage"
        ref="imageRef"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

// ✅ Define props with proper types
interface Props {
  title: string
  description: string
  banner: string
  bgColor?: string
  titleColor?: string
  imageBackgroundColor?: string
  imageWidth?: string
  imageHeight?: string
}

withDefaults(defineProps<Props>(), {
  titleColor: '#253d4e',
  imageBackgroundColor: 'transparent',
  imageWidth: '180px',
  imageHeight: 'auto',
})


// ✅ Type for imageRef
const imageRef = ref<HTMLImageElement | null>(null)
const extractedColor = ref<string | null>(null)

const extractColorFromImage = () => {
  if (!imageRef.value) return

  const canvas = document.createElement('canvas')
  const ctx = canvas.getContext('2d')
  const img = imageRef.value

  if (!ctx || !img) return

  canvas.width = img.naturalWidth || img.width
  canvas.height = img.naturalHeight || img.height
  ctx.drawImage(img, 0, 0)

  try {
    const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height)
    const data = imageData.data
    const colors: { r: number; g: number; b: number }[] = []

    // Sample colors from the image (every 10th pixel for performance)
    for (let i = 0; i < data.length; i += 40) {
      const r = data[i] ?? 255
      const g = data[i + 1] ?? 255
      const b = data[i + 2] ?? 255
      const a = data[i + 3] ?? 255


      if (a > 128) colors.push({ r, g, b })
    }

    if (colors.length > 0) {
      const avgR = Math.round(colors.reduce((sum, c) => sum + c.r, 0) / colors.length)
      const avgG = Math.round(colors.reduce((sum, c) => sum + c.g, 0) / colors.length)
      const avgB = Math.round(colors.reduce((sum, c) => sum + c.b, 0) / colors.length)
      extractedColor.value = `#${avgR.toString(16).padStart(2, '0')}${avgG
        .toString(16)
        .padStart(2, '0')}${avgB.toString(16).padStart(2, '0')}`
    }
  } catch (error) {
    console.warn('Could not extract color from image:', error)
  }
}

onMounted(() => {
  if (imageRef.value?.complete) extractColorFromImage()
})
</script>

<style scoped>
.promotion-card {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 512px;
  height: 300px;
  border-radius: 10px;
  padding: 20px;
  border: 1px solid #eee;
  overflow: hidden;
  transition: all 0.3s ease;
}

.text {
  text-align: left;
  color: #253d4e;
}

.text h3 {
  font-size: 18px;
  font-weight: 600;
  margin-bottom: 8px;
}

.text p {
  font-size: 14px;
  color: #7e7e7e;
  margin-bottom: 12px;
}

.image-container {
  border-radius: 8px;
  padding: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.promotion-card img {
  object-fit: contain;
}
</style>
