<script setup lang="ts">
import IconClear from '@/components/icons/IconClear.vue'
import IconGithub from '@/components/icons/IconGithub.vue'
import IconSearch from '@/components/icons/IconSearch.vue'
import ImageCard from '@/components/ImageCard.vue'
import { ref, type Ref } from 'vue'

const classVariants = ['wide', '', 'big', 'tall']

const searchKeyword = ref('')

const images: Ref<
  {
    src: string
    original: string
    containerClass: string
    randomIndex: number
  }[],
  string[]
> = ref([])

const resetForm = () => {
  searchKeyword.value = ''
  images.value = []
}

const handleSubmit = async (event: Event) => {
  event.preventDefault()

  try {
    const res = await fetch(
      `https://api.pexels.com/v1/search?query=${searchKeyword.value}&per_page=12`,
      {
        headers: {
          Authorization: `${import.meta.env.VITE_APP_PEXELS_API_KEY}`,
        },
      },
    )

    const data = await res.json()
    images.value = data.photos.map(
      (photo: { src: { medium: string; original: string } }) => {
        const randomIndex = Math.floor(Math.random() * 4)
        return {
          src: photo.src.medium as string,
          original: photo.src.original as string,
          containerClass: classVariants[randomIndex],
          randomIndex,
        }
      },
    )
  } catch (error) {
    console.error(error)
  }
}
console.log(images.value.length)
</script>

<template>
  <main>
    <form v-on:submit="handleSubmit">
      <h1 class="title" v-if="images.length === 0">EyeMage</h1>
      <div class="input-container">
        <input
          v-model="searchKeyword"
          class="input"
          placeholder="Search for images"
        />
        <button
          v-if="searchKeyword"
          @click="resetForm"
          class="reset-btn"
          type="button"
        >
          <IconClear />
        </button>
        <button type="submit" class="icon-wrapper">
          <IconSearch />
        </button>
        <div class="github-icon-container">
          <a
            href="https://github.com/sahilxkhadka/eyemage"
            target="_blank"
            class="github-btn"
          >
            <IconGithub />
          </a>
        </div>
      </div>
    </form>
    <div
      class="img-container-default"
      :class="[
        images.length > 0 ? 'img-container' : 'img-container-starting-style',
      ]"
    >
      <ImageCard
        v-for="(image, index) in images"
        :key="index"
        :src="image.src"
        :containerClass="image.containerClass"
        :original="image.original"
      />
    </div>
    <div></div>
  </main>
</template>

<style>
.title {
  display: block;
  font-size: 4rem;
  color: transparent;
  text-align: center;
  font-weight: 900;
  background: linear-gradient(90deg, #00d2ff 0%, #3a47d5 100%);
  background-clip: text;
  height: auto;

  interpolate-size: allow-keywords;
  transition-behavior: allow-discrete;

  transition: all 0.6s ease-out;
}

p {
  color: #fff;
}

.input-container {
  margin: 0 auto 1.5rem auto;
  display: flex;
  gap: 10px;
  align-items: center;
  justify-content: center;
  background: white;
  box-shadow: 0px 12px 24px -1px rgba(215, 112, 235, 0.18);
  width: 576px;
  padding: 0.5rem 1.5rem;
  border-radius: 32px;
  transition: background-color 0.4s ease-in-out;
}

.input-container:hover {
  background: #fff;
}

.icon {
  display: block;
}

.input {
  border: none;
  align-self: stretch;
  background-color: transparent;
  display: block;
  flex: 1;
}

.input:focus {
  border: none;
  outline: none;
}

.input-container:has(.input:focus) {
  animation: squish 2s ease-in 1;
}

button {
  /* all: unset; */
  background: none;
  border: none;
}

button:hover {
  cursor: pointer;
}

.reset-btn {
  color: red;
  font-weight: 700;
  font-size: 20px;
}

@keyframes squish {
  5% {
    transform: scale(1.1, 0.9);
  }
  10% {
    transform: scale(0.9, 1.1) translate(0, -2px);
  }
  15% {
    transform: scale(1);
  }
}

form:has(.input:focus) .title {
  height: 0;
}

.img-container-default {
  interpolate-size: allow-keywords;
  transition-behavior: allow-discrete;
  transition: height 0.6s ease-out;
}

.img-container-starting-style {
  height: 0;
}

.img-container {
  height: 700px;
  /* height: auto; */
  display: grid;
  gap: 12px;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  grid-auto-rows: 200px;
  grid-auto-flow: dense;
}

.img-container > div {
  display: flex;
  justify-content: center;
  align-items: center;
}

.img-container > div > img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 8px;
}

.img-container img {
  width: 100%;
  height: auto;
  max-height: 100%;
  vertical-align: middle;
  display: inline-block;
}

.img-container .wide {
  grid-column: span 2;
}

.img-container .tall {
  grid-row: span 2;
}

.img-container .big {
  grid-column: span 2;
  grid-row: span 2;
}

.github-icon-container {
  display: flex;
  justify-content: center;
  gap: 1.5rem;
}

.github-btn {
  cursor: pointer;
  text-decoration: none;
  color: #ffff;
  width: 40px;
  height: 40px;

  display: flex;
  justify-content: center;
  align-items: center;

  border-radius: 50%;
  background-color: #2d2e32;
  border: 2px solid #2d2e32;
  transition: all 0.45s;
}

.github-btn:hover {
  transform: rotate(360deg);
  transform-origin: center center;
  background-color: #ffff;
  color: #2d2e32;
}

.github-btn:hover .github-svg {
  filter: invert(100%) sepia(100%) saturate(0%) hue-rotate(305deg)
    brightness(103%) contrast(103%);
}
</style>
