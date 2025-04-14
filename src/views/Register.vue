<template>
  <div class="container d-flex align-items-center justify-content-center min-vh-100">
    <div class="card shadow-sm p-4" style="max-width: 400px; width: 100%;">
      <h3 class="text-center mb-4">Register</h3>
      <form @submit.prevent="register">
        <div class="mb-3">
          <label>Name</label>
          <input v-model="name" class="form-control" placeholder="Enter name" />
        </div>
        <div class="mb-3">
          <label>Email</label>
          <input v-model="email" type="email" class="form-control" placeholder="Enter email" />
        </div>
        <div class="mb-3">
          <label>Password</label>
          <input v-model="password" type="password" class="form-control" placeholder="Password" />
        </div>
        <div class="mb-3">
          <label>Confirm Password</label>
          <input v-model="password_confirmation" type="password" class="form-control" placeholder="Confirm Password" />
        </div>
        <button class="btn btn-success w-100">Register</button>
        <p class="text-center mt-3">
          Already have an account?
          <router-link to="/">Login</router-link>
        </p>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import api from '../services/api'
import { useRouter } from 'vue-router'

const name = ref('')
const email = ref('')
const password = ref('')
const password_confirmation = ref('')
const router = useRouter()

const register = async () => {
  try {
    const res = await api.post('/register', {
      name: name.value,
      email: email.value,
      password: password.value,
      password_confirmation: password_confirmation.value
    })
    localStorage.setItem('token', res.data.access_token)
    router.push('/dashboard')
  } catch (err) {
    alert('Registration failed')
  }
}
</script>
