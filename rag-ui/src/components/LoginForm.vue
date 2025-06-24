<template>
  <form @submit.prevent="login">
    <div>
      <label for="username">Username:</label>
      <input id="username" v-model="username" required/>
    </div>
    <div>
      <label for="password">Password:</label>
      <input id="password" type="password" v-model="password" required/>
    </div>
    <button type="submit">Login</button>
    <p v-if="error" class="error">{{ error }}</p>
  </form>
</template>

<script setup>
import {ref} from 'vue'
import axios from 'axios'

const emit = defineEmits(['login-success'])

const username = ref('')
const password = ref('')
const error = ref('')

async function login() {
  error.value = ''
  try {
    const res = await axios.post('/auth/login', {
      username: username.value,
      password: password.value
    })
    emit('login-success', res.data.token)
  } catch (e) {
    error.value = 'Login fehlgeschlagen. Bitte prüfe deine Daten.'
  }
}
</script>

<style scoped>
.error {
  color: red;
  margin-top: 0.5em;
}

form > div {
  margin-bottom: 0.5em;
}
</style>