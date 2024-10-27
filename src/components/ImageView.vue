<script setup lang="ts">
import { ref } from 'vue'
import IconClear from './icons/IconClear.vue'
import { onClickOutside } from '@vueuse/core'

const props = defineProps({
  originalUrl: {
    type: String,
    required: true,
  },
  handleView: {
    type: Function,
    required: true,
  },
  handleDownload: {
    type: Function,
    required: true,
  },
})

const container = ref<HTMLDivElement | null>(null)

onClickOutside(container, () => {
  props.handleView(false)
})
</script>

<template>
  <div class="view-wrapper">
    <div class="view-container" ref="container">
      <button class="close-btn" v-on:click="handleView(false)">
        <IconClear />
      </button>
      <div class="view-image">
        <img :src="originalUrl" alt="" loading="eager" class="selected-image" />
      </div>
      <div class="action-buttons">
        <button class="download-btn" v-on:click="() => handleDownload()">
          <span>Download</span>
        </button>
      </div>
    </div>
  </div>
</template>

<style>
.view-wrapper {
  position: fixed;
  inset: 0;
  z-index: 50;
  height: 100dvh;
  width: 100dvw;
  background-color: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(12px);
  display: flex;
  justify-content: center;
  align-items: center;
}

.view-container {
  margin: 0 auto;
  width: fit-content;
  max-width: 80%;
  /* background-color: white; */
  padding: 1.5rem 2.5rem;
  animation: slideUp 0.3s ease-out forwards;
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
  border-radius: 1rem;
}

.close-btn {
  width: fit-content;
  align-self: flex-end;
  background-color: wheat;
  border-radius: 4px;
}

.view-image {
  flex: 1;
}

.view-image .selected-image {
  display: block;
  width: fit-content;
  margin: 0 auto;
  height: 70dvh;
  object-fit: contain;
  box-shadow:
    rgba(0, 210, 255, 0.4) 0px 10px 15px,
    rgba(58, 71, 213, 0.4) 0px 2px 8px;
}

.action-buttons {
  display: flex;
  justify-content: center;
  gap: 1rem;
  margin-top: 1rem;
}

.download-btn {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 5px;
  background: #00d2ff;
  box-shadow: 0px 6px 24px 0px rgba(0, 0, 0, 0.2);
  overflow: hidden;
  cursor: pointer;
  border: none;
}

.download-btn:after {
  content: ' ';
  width: 0%;
  height: 100%;
  background: #3a47d5;
  position: absolute;
  transition: all 0.4s ease-in-out;
  right: 0;
}

.download-btn:hover::after {
  right: auto;
  left: 0;
  width: 100%;
}

.download-btn span {
  text-align: center;
  text-decoration: none;
  width: 100%;
  padding: 16px 32px;
  color: #fff;
  font-size: 1.25em;
  font-weight: 700;
  z-index: 20;
  transition: all 0.3s ease-in-out;
}

.download-btn:hover span {
  animation: scaleUp 0.3s ease-in-out;
}

@keyframes scaleUp {
  0% {
    transform: scale(1);
  }

  50% {
    transform: scale(0.95);
  }

  100% {
    transform: scale(1);
  }
}

@keyframes slideUp {
  from {
    translate: 0 100%;
  }
  to {
    translate: 0 0;
  }
}
</style>
