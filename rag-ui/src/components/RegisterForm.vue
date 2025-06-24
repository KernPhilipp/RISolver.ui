<template>
  <form @submit.prevent="register">
    <div>
      <label for="username">Username:</label>
      <input id="username" v-model="username" required/>
    </div>
    <div>
      <label for="password">Password:</label>
      <input id="password" type="password" v-model="password" required/>
    </div>
    <button type="submit">Registrieren</button>
    <p v-if="message" :class="messageClass">{{ message }}</p>
  </form>
</template>

<script setup>
import {ref} from 'vue'
import axios from 'axios'

const emit = defineEmits(['registered'])

const username = ref('')
const password = ref('')
const message = ref('')
const messageClass = ref('')

async function register() {
  message.value = ''
  try {
    await axios.post('/auth/register', {
      username: username.value,
      password: password.value
    })
    messageClass.value = 'success'
    message.value = 'Erfolgreich registriert! Bitte nun einloggen.'
    // sag dem Parent, dass es weiter zum Login gehen kann
    emit('registered')
  } catch (e) {
    if (e.response?.status === 409) {
      messageClass.value = 'error'
      message.value = 'Benutzername bereits vergeben.'
    } else {
      messageClass.value = 'error'
      message.value = 'Registrierung fehlgeschlagen.'
    }
  }
}
</script>

<style scoped>
.error {
  color: red;
  margin-top: 0.5em;
}

.success {
  color: green;
  margin-top: 0.5em;
}

form > div {
  margin-bottom: 0.5em;
}
</style>