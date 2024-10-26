<template>
  <div
    ref="container"
    class="parallax-container"
    :class="containerClass"
    :style="transformStyle"
  >
    <img :src="src" :alt="alt" class="grid-image" />
    <div class="parallax-overlay">
      <div class="icons-container">
        <button v-on:click="handleView(true)">
          <IconView />
        </button>
        <button v-on:click="handleDownload">
          <IconDownload />
        </button>
      </div>
    </div>
  </div>
  <ImageView
    v-if="viewImage"
    :handle-view="handleView"
    :original-url="original"
    :handle-download="handleDownload"
  />
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useParallax } from '@vueuse/core'
import type { PropType } from 'vue'
import IconView from './icons/IconView.vue'
import IconDownload from './icons/IconDownload.vue'
import ImageView from './ImageView.vue'

const props = defineProps({
  src: {
    type: String,
    required: true,
  },
  alt: {
    type: String,
    default: '',
  },
  containerClass: {
    type: [String, Object, Array] as PropType<string | object | string[]>,
    default: '',
  },
  scaleConstant: {
    type: Number,
    default: 30,
  },
  original: {
    type: String,
    required: true,
  },
})

const container = ref<HTMLDivElement | null>(null)
const currentRoll = ref(0)
const currentTilt = ref(0)
const viewImage = ref(false)
const { roll, tilt } = useParallax(container)

const transformStyle = computed(() => ({
  transform: `rotateX(${currentRoll.value}deg) rotateY(${currentTilt.value}deg)`,
  transition: 'transform 0.1s ease-out',
}))

let animationFrameId: number | null = null

async function toDataURL(url: string) {
  const blob = await fetch(url).then(res => res.blob())
  return URL.createObjectURL(blob)
}

async function handleDownload() {
  const a = document.createElement('a')
  a.href = await toDataURL(props.original)
  a.download = 'myImage'
  document.body.appendChild(a)
  a.click()
  document.body.removeChild(a)
}

function handleView(view: boolean) {
  if (view) {
    document.body.style.overflowY = 'hidden'
  } else {
    document.body.style.overflowY = 'auto'
  }
  viewImage.value = view
}

const handleMouseMove = () => {
  if (animationFrameId) return
  animationFrameId = requestAnimationFrame(() => {
    currentRoll.value = roll.value * props.scaleConstant
    currentTilt.value = tilt.value * props.scaleConstant
    animationFrameId = null
  })
}

const resetParallax = () => {
  currentRoll.value = 0
  currentTilt.value = 0
  if (animationFrameId) {
    cancelAnimationFrame(animationFrameId)
    animationFrameId = null
  }
}

onMounted(() => {
  container.value?.addEventListener('mousemove', handleMouseMove)
  container.value?.addEventListener('mouseleave', resetParallax)
})

onUnmounted(() => {
  container.value?.removeEventListener('mousemove', handleMouseMove)
  container.value?.removeEventListener('mouseleave', resetParallax)
  if (animationFrameId) {
    cancelAnimationFrame(animationFrameId)
  }
})
</script>

<style>
.parallax-container {
  position: relative;
  overflow: hidden;
}
.parallax-overlay {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border-radius: 8px;

  display: flex;
  justify-content: center;
  align-items: center;

  transform: translate(0, 100%);
  background-image: linear-gradient(
    180deg,
    rgba(0, 210, 255, 0.7) 0%,
    rgba(58, 71, 213, 0.7) 100%
  );
  transition: transform 0.3s ease-in-out;
}

.icons-container {
  display: flex;
  gap: 0.5rem;
}
.parallax-container:hover .parallax-overlay {
  transform: translate(0, 0);
}
</style>
