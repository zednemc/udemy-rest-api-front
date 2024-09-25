<template>
  <q-page padding>
    <p>{{ token }} - {{ expiresIn }}</p>
    <q-btn @click="login">Login</q-btn>
    <q-btn @click="createLink">Create Link</q-btn>

  </q-page>
</template>

<script setup>
import { api } from '../boot/axios.js'
import { ref } from 'vue'

const token = ref('')
const expiresIn = ref('')

defineOptions({
  name: 'IndexPage'
});

const createLink = async () => {
  console.log('createLink')
  try {
    const res = await api({
      url: '/links',
      method: 'POST',
      headers: {
        Authorization: `Bearer ${token.value}`
      },
      data: {
        longLink: 'https://www.bilbomatica.es'
      }
    })
    console.log(res.data)
  } catch (error) {
    console.error(error)
  }
}

const login = async () => {
  console.log('login')

  try {
    const res = await api.post('/auth/login', {
      email: "jaio.ripa@gmail.com",
      password: "123456"
    })
    token.value = res.data.token;
    expiresIn.value = res.data.expiresIn
    setTime()
  } catch (error) {
    console.error(error)
  }
}

const refreshLogin = async () => {
  try {
    const res = await api.get('/auth/refresh')
    token.value = res.data.token;
    expiresIn.value = res.data.expiresIn
    setTime()
  } catch (error) {
    console.error(error)
  }
}
const setTime = () => {
  setTimeout(() => {
    refreshLogin()
  }, expiresIn.value * 1000 - 15000)
}
refreshLogin()
</script>
