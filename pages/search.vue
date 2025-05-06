<template>
  <div class="min-h-screen flex flex-col bg-white">
    <!-- Header with Search -->
    <header class="sticky top-0 bg-white border-b border-gray-200 z-10">
      <div class="container mx-auto px-4 py-3">
        <div class="flex flex-col md:flex-row md:items-center">
          <!-- Logo and Search Bar -->
          <div class="flex items-center flex-grow">
            <div class="mr-6 flex-shrink-0">
              <nuxt-link to="/" class="flex items-center">
                <div class="flex items-center">
                  <div class="w-10 h-10 rounded-full flex items-center justify-center mr-2">
            <img src="/images/logo.svg" alt="LifeSearch Logo" class="w-full h-full" />
                  </div>
                  <div>
                    <h1 class="text-xl font-bold text-black">LifeSearch</h1>
                    <p class="text-xs text-gray-500 -mt-1">by Lifeless</p>
                  </div>
                </div>
              </nuxt-link>
            </div>
            
            <!-- Search Form -->
            <div class="flex-grow max-w-2xl">
              <form @submit.prevent="performSearch" class="relative">
                <input
                  v-model="searchQuery"
                  type="text"
                  class="w-full py-2 px-4 pr-12 border border-gray-300 rounded-full hover:shadow-sm focus:shadow-sm focus:outline-none focus:ring-2 focus:ring-black"
                />
                <button
                  type="button"
                  @click="clearSearch"
                  v-if="searchQuery"
                  class="absolute right-12 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-gray-600"
                >
                  <XIcon class="w-4 h-4" />
                </button>
                <button
                  type="submit"
                  class="absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 hover:text-black p-1"
                >
                  <SearchIcon class="w-4 h-4" />
                </button>
              </form>
            </div>
          </div>
          
          <!-- User Profile -->
          <div class="mt-3 md:mt-0 md:ml-4 flex items-center">
            <button class="p-2 text-gray-600 hover:bg-gray-100 rounded-full">
              <SettingsIcon class="w-5 h-5" />
            </button>
            <button class="ml-2 p-2 text-gray-600 hover:bg-gray-100 rounded-full">
              <UserIcon class="w-5 h-5" />
            </button>
          </div>
        </div>
        
        <!-- Search Categories -->
        <div class="mt-3 flex space-x-6 text-sm border-b border-gray-200">
          <button class="pb-2 px-1 text-black border-b-2 border-black font-medium flex items-center">
            <SearchIcon class="w-4 h-4 mr-1" />
            <span>Semua</span>
          </button>
          <button class="pb-2 px-1 text-gray-600 hover:text-black flex items-center">
            <ImageIcon class="w-4 h-4 mr-1" />
            <span>Gambar</span>
          </button>
          <button class="pb-2 px-1 text-gray-600 hover:text-black flex items-center">
            <MapPinIcon class="w-4 h-4 mr-1" />
            <span>Maps</span>
          </button>
          <button class="pb-2 px-1 text-gray-600 hover:text-black flex items-center">
            <NewspaperIcon class="w-4 h-4 mr-1" />
            <span>Berita</span>
          </button>
          <button class="pb-2 px-1 text-gray-600 hover:text-black flex items-center">
            <VideoIcon class="w-4 h-4 mr-1" />
            <span>Video</span>
          </button>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="flex-grow container mx-auto px-4 py-4">
      <!-- Search Stats -->
      <div class="text-sm text-gray-600 mb-5" v-if="!isLoading && searchResults.length > 0">
        Sekitar {{ searchResults.length * 1000 }} hasil ({{ (Math.random() * 0.5 + 0.1).toFixed(2) }} detik)
      </div>
      
      <!-- Loading State -->
      <div v-if="isLoading" class="flex justify-center my-12">
        <div class="flex flex-col items-center">
          <LoaderIcon class="w-10 h-10 text-black animate-spin" />
          <p class="mt-4 text-gray-600">Mencari hasil...</p>
        </div>
      </div>

      <!-- Error Message -->
      <div v-else-if="error" class="bg-gray-100 border border-gray-300 rounded-lg p-4 my-6">
        <div class="flex">
          <AlertCircleIcon class="w-5 h-5 text-black mr-3 flex-shrink-0" />
          <p class="text-gray-700">{{ error }}</p>
        </div>
      </div>

      <!-- Search Results -->
      <div v-else-if="searchResults.length > 0" class="space-y-8 max-w-3xl">
        <div v-for="(result, index) in searchResults" :key="index" class="result">
          <div class="mb-1 flex items-center">
            <img v-if="result.favicon" :src="result.favicon" alt="Site icon" class="w-4 h-4 mr-2" />
            <a :href="result.url" class="text-sm text-gray-600 hover:underline truncate">{{ result.url }}</a>
          </div>
          <h3 class="text-xl text-black hover:underline mb-1">
            <a :href="result.url" target="_blank">{{ result.title }}</a>
          </h3>
          <p class="text-sm text-gray-700">{{ result.description }}</p>
        </div>
        
        <!-- Pagination -->
        <div class="mt-10 flex justify-center">
          <div class="inline-flex items-center">
            <button class="px-4 py-2 text-black font-medium">
              <ChevronLeftIcon class="w-5 h-5" />
            </button>
            <div class="flex space-x-1">
              <button class="w-8 h-8 flex items-center justify-center rounded-full bg-black text-white">1</button>
              <button class="w-8 h-8 flex items-center justify-center rounded-full hover:bg-gray-100 text-black">2</button>
              <button class="w-8 h-8 flex items-center justify-center rounded-full hover:bg-gray-100 text-black">3</button>
              <button class="w-8 h-8 flex items-center justify-center rounded-full hover:bg-gray-100 text-black">4</button>
              <button class="w-8 h-8 flex items-center justify-center rounded-full hover:bg-gray-100 text-black">5</button>
              <span class="w-8 h-8 flex items-center justify-center">...</span>
              <button class="w-8 h-8 flex items-center justify-center rounded-full hover:bg-gray-100 text-black">10</button>
            </div>
            <button class="px-4 py-2 text-black font-medium">
              <ChevronRightIcon class="w-5 h-5" />
            </button>
          </div>
        </div>
      </div>

      <!-- No Results -->
      <div v-else class="text-center my-16">
        <FileSearchIcon class="w-16 h-16 text-gray-400 mx-auto mb-4" />
        <h3 class="text-xl font-medium text-gray-700 mb-2">Tidak ada hasil ditemukan</h3>
        <p class="text-gray-500">
          Coba gunakan kata kunci yang berbeda atau lebih umum
        </p>
      </div>
    </main>

    <!-- Footer -->
    <footer class="py-4 bg-gray-100 border-t border-gray-200 mt-auto">
      <div class="container mx-auto px-4">
        <div class="flex flex-col md:flex-row justify-between items-center">
          <div class="mb-4 md:mb-0">
            <p class="text-gray-500 text-sm">Indonesia</p>
          </div>
          <div class="flex flex-wrap justify-center gap-x-6 gap-y-2">
            <a href="#" class="text-gray-600 hover:text-black text-sm">Bantuan</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Kirim Masukan</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Privasi</a>
            <a href="#" class="text-gray-600 hover:text-black text-sm">Persyaratan</a>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'nuxt/app'
import { SearchIcon, XIcon, SettingsIcon, UserIcon, ImageIcon, MapPinIcon, NewspaperIcon, VideoIcon, LoaderIcon, AlertCircleIcon, FileSearchIcon, ChevronLeftIcon, ChevronRightIcon, CompassIcon } from 'lucide-vue-next'

const route = useRoute()
const router = useRouter()
const searchQuery = ref('')
const searchResults = ref([])
const isLoading = ref(false) // Initialize to false
const error = ref(null)

// Mock search results data
const mockSearchData = [
  {
    title: 'Nuxt.js - Kerangka Kerja Vue.js Intuitif',
    url: 'https://nuxt.com',
    description: 'Nuxt adalah kerangka kerja Vue.js yang intuitif. Ini memudahkan pembuatan aplikasi Vue.js dengan server-side rendering, static site generation, dan banyak fitur lainnya.',
    favicon: 'https://nuxt.com/icon.png'
  },
  {
    title: 'Vue.js - Framework JavaScript Progresif',
    url: 'https://vuejs.org',
    description: 'Vue.js adalah framework JavaScript progresif untuk membangun antarmuka pengguna. Tidak seperti framework monolitik lainnya, Vue dirancang dari awal untuk dapat diadopsi secara bertahap.',
    favicon: 'https://vuejs.org/logo.png'
  },
  {
    title: 'Tailwind CSS - Framework CSS Utility-First',
    url: 'https://tailwindcss.com',
    description: 'Tailwind CSS adalah framework CSS utility-first untuk membangun desain kustom dengan cepat tanpa meninggalkan HTML Anda.',
    favicon: 'https://tailwindcss.com/favicon.ico'
  },
  {
    title: 'JavaScript - MDN Web Docs',
    url: 'https://developer.mozilla.org/en-US/docs/Web/JavaScript',
    description: 'JavaScript (JS) adalah bahasa pemrograman yang ringan, ditafsirkan, atau just-in-time dikompilasi dengan fungsi kelas satu.',
    favicon: 'https://developer.mozilla.org/favicon.ico'
  },
  {
    title: 'TypeScript: JavaScript dengan Sintaks untuk Tipe',
    url: 'https://www.typescriptlang.org',
    description: 'TypeScript adalah JavaScript dengan sintaks untuk tipe. TypeScript adalah bahasa pemrograman open-source yang dibangun di atas JavaScript.',
    favicon: 'https://www.typescriptlang.org/favicon.ico'
  },
  {
    title: 'GitHub: Where the world builds software',
    url: 'https://github.com',
    description: 'GitHub adalah platform pengembangan perangkat lunak yang memungkinkan Anda menyimpan, melacak, dan berkolaborasi pada proyek perangkat lunak.',
    favicon: 'https://github.com/favicon.ico'
  },
  {
    title: 'Stack Overflow - Where Developers Learn, Share, & Build',
    url: 'https://stackoverflow.com',
    description: 'Stack Overflow adalah komunitas pengembang terbesar dan paling tepercaya untuk berbagi pengetahuan pemrograman, membangun karier mereka, dan memecahkan masalah pemrograman mereka.',
    favicon: 'https://stackoverflow.com/favicon.ico'
  }
]

// Simulate API call with delay
const searchAPI = async (query) => {
  // Simulate network delay
  await new Promise(resolve => setTimeout(resolve, 1200))
  
  // Simulate search logic
  if (!query.trim()) {
    return []
  }
  
  // Simulate random error (1 in 10 chance)
  if (Math.random() < 0.1) {
    throw new Error('Terjadi kesalahan saat mencari. Silakan coba lagi.')
  }
  
  // Filter mock data based on query
  const normalizedQuery = query.toLowerCase()
  return mockSearchData.filter(item => 
    item.title.toLowerCase().includes(normalizedQuery) || 
    item.description.toLowerCase().includes(normalizedQuery)
  )
}

// Search function
const performSearch = async () => {
  if (!searchQuery.value.trim()) return
  
  // Update URL with new search query
  router.push(`/search?q=${encodeURIComponent(searchQuery.value.trim())}`)
  
  isLoading.value = true
  error.value = null
  
  try {
    searchResults.value = await searchAPI(searchQuery.value)
  } catch (err) {
    error.value = err.message
    searchResults.value = []
  } finally {
    isLoading.value = false
  }
}

const clearSearch = () => {
  searchQuery.value = ''
}

// Initialize search from URL query parameter
const initializeSearch = async () => {
  const query = route.query.q
  if (query) {
    searchQuery.value = query.toString()
    isLoading.value = true // Set loading to true before search
    try {
      searchResults.value = await searchAPI(searchQuery.value)
    } catch (err) {
      error.value = err.message
    } finally {
      isLoading.value = false
    }
  }
}

onMounted(async () => {
  if (route.query.q) {
    await initializeSearch();
  } else {
    isLoading.value = false; // Ensure loading is set to false if no query
  }
});
</script>