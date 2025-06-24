<template>
  <div>
    <form @submit.prevent="ask">
      <input
          v-model="question"
          placeholder="Deine Frage"
          required
          :disabled="loading"
      />
      <button type="submit" :disabled="loading">
        {{ loading ? 'Lädt…' : 'Fragen' }}
      </button>
    </form>

    <div v-if="loading" class="loading">
      Antwort wird generiert…
    </div>

    <div v-if="!loading && answer">
      <h3>Antwort:</h3>
      <p>{{ answer }}</p>
    </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'
import axios from 'axios'

const props = defineProps({token: String})

const question = ref('')
const answer = ref('')
const loading = ref(false)

const ask = async () => {
  console.log('Frontend – sende Frage:', question.value)
  answer.value = ''
  loading.value = true

  try {
    const response = await axios.post(
        '/api/rag',
        {question: question.value},
        {headers: {Authorization: `Bearer ${props.token}`}}
    )

    const raw = typeof response.data === 'string'
        ? response.data
        : response.data.answer || ''

    const lines = raw.split('\n')
    const filtered = lines
        .filter(line => {
          const t = line.trim()
          if (!t) return false
          if (t.startsWith('>')) return false
          const low = t.toLowerCase()
          if (low.includes('langchaindeprecationwarning')) return false
          if (/^[a-z]:\\/.test(t.toLowerCase())) return false
          return true
        })
        .join('\n')

    answer.value = filtered
  } catch (error) {
    console.error('RAG request failed:', error)
    answer.value = 'Fehler beim Abrufen der Antwort'
  } finally {
    loading.value = false
  }
}
</script>

<style scoped>
.loading {
  margin-top: 1em;
  font-style: italic;
  color: #555;
}

form > * {
  margin-right: 0.5em;
}
</style>