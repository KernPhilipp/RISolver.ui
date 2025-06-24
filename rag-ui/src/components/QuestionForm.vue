<script setup>
import {ref} from 'vue'
import axios from 'axios'

const props = defineProps({token: String})
const question = ref('')
const answer = ref('')

const ask = async () => {
  try {
    const res = await axios.post('/api/rag',
        {question: question.value},
        {headers: {Authorization: `Bearer ${props.token}`}}
    )
    answer.value = res.data.answer
  } catch {
    answer.value = 'Fehler beim Abrufen der Antwort'
  }
}
</script>

<template>
  <div>
    <form @submit.prevent="ask">
      <input v-model="question" placeholder="Deine Frage" required/>
      <button type="submit">Fragen</button>
    </form>
    <div v-if="answer">
      <h3>Antwort:</h3>
      <p>{{ answer }}</p>
    </div>
  </div>
</template>

<style scoped>

</style>