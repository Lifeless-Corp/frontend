<template>
  <div class="container mx-auto px-4 py-8">
    <div v-if="article">
      <h1 class="text-2xl font-bold mb-2">{{ article.title }}</h1>
      <p class="mb-4 text-gray-600">{{ article.authors?.map(author => author.full_name).join(', ') }}</p>
      <div class="mb-6">
        <span class="text-sm text-gray-500">{{ article.journal.title }}</span>
      </div>
      <h2 class="text-lg font-bold mb-2">Abstract</h2>
      <div class="prose max-w-none mb-4">
        <p>{{ article.abstract }}</p>
      </div>
      <h2 class="text-lg font-bold mb-2">Full Text</h2>
      <div class="prose max-w-none">
        <p>{{ article.full_text }}</p>
      </div>
      <a v-if="article.url" :href="article.url" target="_blank" class="text-blue-600 underline mt-4 inline-block">Lihat sumber asli</a>
    </div>
    <div v-else>
      <p>Memuat artikel...</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'nuxt/app'

const route = useRoute()
const article = ref(null)

onMounted(async () => {
  const pmid = route.params.pmid
  const config = useRuntimeConfig()
  const res = await fetch(`${config.public.NUXT_PUBLIC_API_URL}/articles/${pmid}`)
  article.value = await res.json()
})
</script>