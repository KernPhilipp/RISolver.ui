<template>
  <LoginForm v-if="!token" @login-success="onLogin"/>
  <QuestionForm v-else :token="token"/>
</template>

<script setup>
import {ref} from 'vue'
import axios from 'axios'
import LoginForm from './components/LoginForm.vue'
import QuestionForm from './components/QuestionForm.vue'

const token = ref('')

function onLogin(newToken) {
  token.value = newToken
  axios.defaults.headers.common['Authorization'] = `Bearer ${newToken}`
}
</script>