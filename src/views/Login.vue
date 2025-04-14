<template>
  <div class="container d-flex align-items-center justify-content-center min-vh-100">
    <div class="card shadow-sm p-4" style="max-width: 400px; width: 100%;">
      <h3 class="text-center mb-4">Login</h3>
      <form @submit.prevent="login">
        <div class="mb-3">
          <label>Email</label>
          <input v-model="email" type="email" class="form-control" placeholder="Enter email" />
        </div>
        <div class="mb-3">
          <label>Password</label>
          <input v-model="password" type="password" class="form-control" placeholder="Enter password" />
        </div>
        <button class="btn btn-primary w-100">Login</button>
        <p class="text-center mt-3">
          Don’t have an account?
          <router-link to="/register">Register</router-link>
        </p>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import api from '../services/api'
import { useRouter } from 'vue-router'

const email = ref('')
const password = ref('')
const router = useRouter()

const login = async () => {
  try {
    const res = await api.post('/login', {
      email: email.value,
      password: password.value
    })
    localStorage.setItem('token', res.data.access_token)
    router.push('/dashboard')
  } catch (err) {
    alert('Invalid credentials!')
  }
}
</script>
