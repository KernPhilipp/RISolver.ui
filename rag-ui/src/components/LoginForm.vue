<script setup>
import {ref} from 'vue'
import axios from 'axios'

const emit = defineEmits(['login-success'])
const username = ref('')
const password = ref('')
const error = ref('')

const login = async () => {
  try {
    const res = await axios.post('/auth/login', {
      username: username.value,
      password: password.value
    })
    emit('login-success', res.data.token)
  } catch {
    error.value = 'Login fehlgeschlagen'
  }
}
</script>

<template>
  <form @submit.prevent="login">
    <div>
      <label>Username:</label>
      <input v-model="username" required/>
    </div>
    <div>
      <label>Password:</label>
      <input type="password" v-model="password" required/>
    </div>
    <button type="submit">Login</button>
    <p v-if="error">{{ error }}</p>
  </form>
</template>

<style scoped>

</style>