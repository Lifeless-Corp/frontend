<template>
  <div class="min-h-screen flex flex-col bg-white">
    <!-- Main Content -->
    <main class="flex-grow flex flex-col items-center justify-center px-4">
      <!-- Logo -->
      <div class="mb-8">
        <div class="flex flex-col items-center justify-center">
          <div class="mb-4 flex items-center justify-center w-24 h-24 rounded-full shadow-lg">
            <!-- Ganti CompassIcon dengan img SVG -->
            <img src="/images/logo.svg" alt="Organa Logo" class="w-full h-full" />
          </div>
          <h1 class="text-5xl font-bold text-black">Organa</h1>
        </div>
      </div>

      <!-- Search Form -->
      <div class="w-full max-w-2xl">
        <form @submit.prevent="handleSearch" class="relative">
          <div class="relative">
            <input
              v-model="searchQuery"
              type="text"
              placeholder="Cari di web..."
              class="w-full py-4 px-5 pr-12 rounded-full border border-gray-300 hover:shadow-md focus:shadow-md focus:outline-none focus:ring-2 focus:ring-black transition-shadow text-lg"
            />
            <button
              type="button"
              @click="clearSearch"
              v-if="searchQuery"
              class="absolute right-14 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600"
            >
              <XIcon class="w-5 h-5" />
            </button>
            <button
              type="submit"
              class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-black p-2"
            >
              <SearchIcon class="w-5 h-5" />
            </button>
          </div>
        </form>
      </div>

      <!-- Search Buttons -->
      <div class="mt-8 flex space-x-4">
        <button
          @click="handleSearch"
          class="px-6 py-2 bg-black hover:bg-gray-800 text-white rounded text-sm font-medium transition-colors"
        >
          Telusuri
        </button>
        <button
          @click="handleLuckySearch"
          class="px-6 py-2 bg-gray-100 hover:bg-gray-200 text-black rounded text-sm font-medium transition-colors border border-gray-300"
        >
          Saya Lagi Beruntung
        </button>
      </div>
    </main>

    <!-- Footer -->
    <footer class="py-4 bg-gray-100 border-t border-gray-200">
      <div class="container mx-auto px-4">
        <div class="flex flex-col md:flex-row justify-between items-center">
          <div class="mb-4 md:mb-0">
            <p class="text-gray-500 text-sm">Indonesia</p>
          </div>
          <div class="flex flex-wrap justify-center gap-x-6 gap-y-2">
            <a href="#" class="text-gray-600 hover:text-black text-sm">Tentang</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Periklanan</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Bisnis</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Cara Kerja Penelusuran</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Privasi</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Persyaratan</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Setelan</a>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'nuxt/app'
import { SearchIcon, XIcon, CompassIcon } from 'lucide-vue-next'

const router = useRouter()
const searchQuery = ref('')

const handleSearch = () => {
  if (searchQuery.value.trim()) {
    router.push(`/search?q=${encodeURIComponent(searchQuery.value.trim())}`)
  }
}

const handleLuckySearch = () => {
  // Redirect to a random website. For demo purposes, redirect to nuxt.com
  window.location.href = 'https://nuxt.com'
}

const clearSearch = () => {
  searchQuery.value = ''
}
</script>