<template>
  <div class="bg-gray-100">
    <div class="min-h-screen max-w-screen-xl mx-auto px-3">
      <nav class="flex flex-col sm:flex-row justify-between py-6 lg:border-b-2 lg:border-gray-200">
        <div class="flex items-center mx-auto mb-6 sm:mx-0 sm:mb-0">
          <svg class="mr-1 h-12 w-auto" viewBox="0 0 64 64" fill="none" xmlns="http://www.w3.org/2000/svg">
            <title>TailwindCSS Generator</title>
            <path fill-rule="evenodd" clip-rule="evenodd" d="M32 16C24.8 16 20.3 19.6 18.5 26.8C21.2 23.2 24.35 21.85 27.95 22.75C30.004 23.2635 31.4721 24.7536 33.0971 26.4031C35.7443 29.0901 38.8081 32.2 45.5 32.2C52.7 32.2 57.2 28.6 59 21.4C56.3 25 53.15 26.35 49.55 25.45C47.496 24.9365 46.0279 23.4464 44.4029 21.7969C41.7557 19.1099 38.6919 16 32 16ZM18.5 32.2C11.3 32.2 6.8 35.8 5 43C7.7 39.4 10.85 38.05 14.45 38.95C16.504 39.4635 17.9721 40.9536 19.5971 42.6031C22.2443 45.2901 25.3081 48.4 32 48.4C39.2 48.4 43.7 44.8 45.5 37.6C42.8 41.2 39.65 42.55 36.05 41.65C33.996 41.1365 32.5279 39.6464 30.9029 37.9969C28.2557 35.3099 25.1919 32.2 18.5 32.2Z" fill="url(#paint0_linear)" />
            <defs>
              <linearGradient
                id="paint0_linear"
                x1="3.5"
                y1="16"
                x2="59"
                y2="48"
                gradientUnits="userSpaceOnUse"
              >
                <stop stop-color="#38a169" />
                <stop offset="1" stop-color="#9ae6b4" />
              </linearGradient>
            </defs>
          </svg>
          <p class="text-2xl font-semibold text-gray-800 mb-1">
            tailwindcss generator
          </p>
        </div>
        <div class="flex items-center mx-auto sm:mx-0">
          <button
            v-if="!loading"
            class="rounded-lg px-4 py-3 bg-white hover:bg-gray-200 md:text-lg xl:text-base text-gray-800 font-semibold leading-tight shadow-md"
            @click="addColor"
          >
            Add Color
          </button>
          <button
            v-if="generateVisible && !loading"
            class="ml-4 rounded-lg px-4 py-3 bg-green-500 hover:bg-green-600 md:text-lg xl:text-base text-white font-semibold leading-tight shadow-md"
            @click="generateTailwind"
          >
            Generate CSS File
          </button>
          <button
            v-if="generateVisible && loading"
            class="loading ml-4 rounded-lg px-4 md:px-5 xl:px-4 py-3 md:py-4 xl:py-3 bg-green-500 hover:bg-green-600 md:text-lg xl:text-base text-white font-semibold leading-tight shadow-md"
          />
        </div>
      </nav>
      <div class="py-10 text-center max-w-xl mx-auto">
        <h1 class="text-3xl tracking-tight sm:text-4xl md:text-5xl xl:text-4xl font-medium leading-tight text-gray-800">
          Generate a Tailwind CSS file using <span class="sm:block text-green-500 font-medium">a custom color scheme.</span>
        </h1>
        <p class="mt-6 leading-relaxed sm:text-lg md:text-xl xl:text-lg text-gray-600">
          For when you want Tailwind CSS in a project that doesn't have a build step but does have a specific color scheme
        </p>
      </div>
      <div class="flex flex-wrap">
        <div v-for="(color, index) in colors" :key="index" class="w-full sm:w-1/2 md:w-1/3 lg:w-1/4 p-3">
          <div class="p-6 bg-white border border-gray-400 rounded-lg relative">
            <button class="absolute right-0 px-6 text-gray-600" @click="deleteColor(index)">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                :width="16"
                :height="16"
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                stroke-width="2"
                stroke-linecap="round"
                stroke-linejoin="round"
              ><line x1="18" y1="6" x2="6" y2="18" /><line x1="6" y1="6" x2="18" y2="18" /></svg>
            </button>
            <div class="mb-4">
              <label class="block text-gray-600 text-sm font-bold mb-2">
                Color Name
              </label>
              <input v-model="color.name" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-600 leading-tight focus:outline-none focus:shadow-outline" type="text">
            </div>
            <div class="mb-4">
              <label class="block text-gray-600 text-sm font-bold mb-2">
                Hex Code
              </label>
              <input v-model="color.hex" class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-600 leading-tight focus:outline-none focus:shadow-outline" type="text">
            </div>
            <div class="border rounded shadow h-16 w-full" :style="{ background: formatColor(color.hex) }" />
          </div>
        </div>
      </div>
      <footer class="py-6 mt-12">
        <p class="text-gray-600 text-center">
          A useful tool created by <a target="_blank" href="https://shadowsmith.com" class="text-green-600 font-semibold">Shadow Smith</a>
        </p>
      </footer>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      loading: false,
      colors: [
        {
          name: 'Black',
          hex: '#000000'
        },
        {
          name: 'White',
          hex: '#ffffff'
        }
      ],
      tailwind: ''
    }
  },
  computed: {
    activeColors () {
      return this.colors.filter((color) => {
        return color.name && color.hex
      })
    },
    generateVisible () {
      if (this.activeColors.length > 0) {
        return true
      }

      return false
    }
  },
  methods: {
    addColor () {
      this.colors.push({
        name: '',
        hex: ''
      })
    },
    deleteColor (index) {
      this.colors = this.colors.filter((color, i) => {
        return i !== index
      })
    },
    formatColor (hex) {
      if (!hex.includes('#')) {
        return `#${hex}`
      }

      return hex
    },
    async generateTailwind () {
      this.loading = true

      if (this.activeColors.length > 0) {
        // Transform the array of active colors into the Tailwind config format
        const colors = this.activeColors.reduce((acc, color) => {
          const { hex } = color
          let { name } = color

          // Transform the name of the color into all lowercase
          name = name.toLowerCase().split(' ').join('')

          return {
            ...acc,
            [name]: `#${hex}`
          }
        }, {})

        const response = await axios.post('https://tailwind-generator.herokuapp.com/', {
          colors
        })

        this.tailwind = response.data

        const data = this.tailwind
        const dataUri = `data:application/json;charset=utf-8,${encodeURIComponent(data)}`
        const linkElement = document.createElement('a')
        linkElement.setAttribute('href', dataUri)
        linkElement.setAttribute('download', 'tailwind.css')
        linkElement.click()
      }

      this.loading = false
    }
  }
}
</script>

<style scoped>
nav button {
  height: 44px;
}

.loading::after {
    border-color: transparent transparent #fff #fff;
    position: relative;
    left: calc(50% - (1em / 2));
    top: calc(50% - (1em / 2));
    -webkit-animation: spinAround .5s infinite linear;
    animation: spinAround .5s infinite linear;
    border: 2px solid #dbdbdb;
    border-radius: 290486px;
    border-right-color: transparent;
    border-top-color: transparent;
    content: "";
    display: block;
    height: 1em;
    width: 1em;
}

@-webkit-keyframes spinAround {
  from {
    transform: rotate(0)
  }
  to {
    transform: rotate(359deg)
  }
}

@keyframes spinAround {
  from {
    transform: rotate(0)
  }
  to {
    transform: rotate(359deg)
  }
}
</style>
