<template>
  <div class="min-h-screen flex justify-center items-center">
    <button @click="saveTailwind" class="py-2 px-4 rounded bg-gray-300 hover:bg-gray-400">
      Save TailwindCSS File
    </button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data () {
    return {
      tailwind: ''
    }
  },
  methods: {
    async saveTailwind () {
      const response = await axios.post('https://tailwind-generator.herokuapp.com/', {
        colors: {
          purple: '#9400D3'
        }
      })

      this.tailwind = response.data

      const data = this.tailwind
      const dataUri = `data:application/json;charset=utf-8,${encodeURIComponent(data)}`
      const linkElement = document.createElement('a')
      linkElement.setAttribute('href', dataUri)
      linkElement.setAttribute('download', 'tailwind.css')
      linkElement.click()
    }
  }
}
</script>
