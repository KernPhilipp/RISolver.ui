<template>
  <div>
    <div v-if="mode === 'register'">
      <RegisterForm @registered="switchToLogin"/>
      <p>
        Schon einen Account?
        <button @click="mode = 'login'">Einloggen</button>
      </p>
      <p v-if="msg" class="info">{{ msg }}</p>
    </div>

    <div v-else-if="mode === 'login'">
      <LoginForm @login-success="onLogin"/>
      <p>
        Noch keinen Account?
        <button @click="mode = 'register'">Registrieren</button>
      </p>
      <p v-if="msg" class="info">{{ msg }}</p>
    </div>

    <div v-else-if="mode === 'authenticated'">
      <QuestionForm :token="token"/>

      <div class="actions">
        <button @click="logout">Abmelden</button>
        <button @click="mode = 'update'">Benutzer ändern</button>
        <button @click="deleteUser">Konto löschen</button>
      </div>
    </div>

    <div v-else-if="mode === 'update'">
      <h3>Profil aktualisieren</h3>
      <form @submit.prevent="updateUser">
        <div>
          <label>Neuer Username:</label>
          <input v-model="updateReq.username" placeholder="Leer = unverändert"/>
        </div>
        <div>
          <label>Neues Passwort:</label>
          <input type="password" v-model="updateReq.password" placeholder="Leer = unverändert"/>
        </div>
        <button type="submit">Speichern</button>
        <button type="button" @click="mode = 'authenticated'">Abbrechen</button>
      </form>
    </div>
  </div>
</template>

<script setup>
import {ref} from 'vue'
import axios from 'axios'

import RegisterForm from './components/RegisterForm.vue'
import LoginForm from './components/LoginForm.vue'
import QuestionForm from './components/QuestionForm.vue'

const mode = ref('register')
const token = ref('')
const updateReq = ref({username: '', password: ''})
const msg = ref('')

function switchToLogin() {
  mode.value = 'login'
}

function onLogin(newToken) {
  token.value = newToken
  axios.defaults.headers.common['Authorization'] = `Bearer ${newToken}`
  msg.value = ''
  mode.value = 'authenticated'
}

function logout() {
  token.value = ''
  delete axios.defaults.headers.common['Authorization']
  msg.value = ''
  mode.value = 'login'
}

async function deleteUser() {
  try {
    await axios.delete('/auth/user')
    logout()
    msg.value = 'Konto gelöscht. Bitte registriere dich erneut.'
  } catch {
    msg.value = 'Fehler beim Löschen des Kontos.'
    mode.value = 'login'
  }
}

async function updateUser() {
  try {
    await axios.put('/auth/user', updateReq.value)
    logout()
    msg.value = 'Daten aktualisiert – bitte logge dich erneut ein.'
  } catch (e) {
    if (e.response?.status === 409) {
      msg.value = 'Username bereits vergeben.'
    } else {
      msg.value = 'Fehler beim Aktualisieren.'
    }
    mode.value = 'login'
  }
}
</script>

<style scoped>
.actions button {
  margin-right: 0.5em;
  margin-top: 1em;
}

form > div {
  margin-bottom: 0.5em;
}

.info {
  margin-top: 1em;
  font-weight: bold;
}
</style>