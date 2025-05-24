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
                  <img src="/images/logo.svg" alt="Organa Logo" class="w-full h-full" />
                  </div>
                  <div>
                    <h1 class="text-xl font-bold text-black">Organa</h1>
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
      
      <!-- LLM Analysis (AI Overview) -->
      <div v-if="searchResults.length > 0" class="mb-6">
        <LlmAnalysis 
          :query="searchQuery"
          :documents="searchResults"
        />
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
        <div v-for="(result, index) in searchResults" :key="index" class="result border-b border-gray-200 pb-6">
          <div class="mb-1 flex items-center">
            <img v-if="result.favicon" :src="result.favicon" alt="Site icon" class="w-4 h-4 mr-2" />
            <a :href="result.url" class="text-sm text-gray-600 hover:underline truncate">
              {{ result.pmid ? `PMID: ${result.pmid}` : result.url }}
            </a>
          </div>
          <h3 class="text-xl text-black hover:underline mb-1">
            <a :href="result.url" target="_blank">{{ result.title }}</a>
          </h3>
          
          <!-- Authors if available -->
          <p class="text-sm text-gray-700 mb-2" v-if="result.authors && result.authors.length">
            {{ result.authors.join(', ') }}
          </p>
          
          <!-- Journal if available -->
          <p class="text-sm text-gray-500 mb-2" v-if="result.journal">
            {{ result.journal }}
          </p>
          
          <!-- Abstract with show more/less -->
          <div v-if="result.abstract" class="mb-3">
            <p class="text-sm text-gray-700">
              {{ truncateAbstract(result.abstract, index) }}
            </p>
            <button 
              v-if="result.abstract && result.abstract.length > 300" 
              class="text-blue-600 text-sm mt-1 hover:underline"
              @click="toggleAbstract(index)"
            >
              {{ expandedAbstracts[index] ? 'Show less' : 'Show more' }}
            </button>
          </div>
          
          <!-- Description if no abstract -->
          <p v-else-if="result.description" class="text-sm text-gray-700 mb-3">
            {{ result.description }}
          </p>
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
import { SearchIcon, XIcon, SettingsIcon, UserIcon, ImageIcon, MapPinIcon, NewspaperIcon, VideoIcon, LoaderIcon, AlertCircleIcon, FileSearchIcon, ChevronLeftIcon, ChevronRightIcon } from 'lucide-vue-next'
import LlmAnalysis from '~/components/LlmAnalysis.vue'

const route = useRoute()
const router = useRouter()
const searchQuery = ref('')
const searchResults = ref([])
const isLoading = ref(false) 
const error = ref(null)
const expandedAbstracts = ref({}) // Add this to track expanded abstracts

// Replace mock implementation with real API call
const searchAPI = async (query) => {
  if (!query.trim()) {
    return []
  }
  
  try {
    // Assume the API endpoint is at /api/search with a query parameter
    const config = useRuntimeConfig()
    const response = await fetch(`${config.public.NUXT_PUBLIC_API_URL}/articles/search?query=${encodeURIComponent(query.trim())}`)
    
    if (!response.ok) {
      // Handle HTTP errors
      throw new Error(`Error: ${response.status} - ${response.statusText}`)
    }
    
    const data = await response.json()
    return data.results || [] // Adjust based on your API response structure
  } catch (err) {
    console.error('Search API error:', err)
    throw err
  }
}

// Search function
const performSearch = async () => {
  if (!searchQuery.value.trim()) return
  
  try {
    // Update URL with new search query
    router.push(`/search?q=${encodeURIComponent(searchQuery.value.trim())}`)
    
    isLoading.value = true
    error.value = null
    
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
    isLoading.value = true
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
    isLoading.value = false;
  }
});

// Function to toggle abstract expansion
function toggleAbstract(index) {
  expandedAbstracts.value[index] = !expandedAbstracts.value[index]
}

// Function to truncate abstract with proper showing/hiding
function truncateAbstract(abstract, index) {
  if (expandedAbstracts.value[index]) {
    return abstract
  }
  return abstract.length > 300 ? abstract.substring(0, 300) + '...' : abstract
}
</script>