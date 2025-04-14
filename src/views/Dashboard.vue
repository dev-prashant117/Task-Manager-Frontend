<template>
  <div>
    <!-- Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark mb-4">
      <div class="container-fluid">
        <span class="navbar-brand">Task Manager</span>
        <div class="d-flex">
          <button class="btn btn-outline-light" @click="logout">Logout</button>
        </div>
      </div>
    </nav>

    <!-- Main content -->
    <div class="container">
      <h2 class="mb-3">Your Tasks</h2>

      <!-- Task form (create/update) -->
      <div class="card mb-4">
        <div class="card-body">
          <h5 class="card-title">{{ editing ? 'Edit Task' : 'Add New Task' }}</h5>
          <form @submit.prevent="editing ? updateTask() : createTask()">
            <div class="mb-3">
              <label>Title</label>
              <input v-model="form.title" class="form-control" required />
            </div>
            <div class="mb-3">
              <label>Description</label>
              <textarea v-model="form.description" class="form-control" rows="2"></textarea>
            </div>
            <div class="mb-3">
              <label>Due Date</label>
              <input v-model="form.due_date" type="date" class="form-control" required />
            </div>
            <div class="mb-3">
              <label>Status</label>
              <select v-model="form.status" class="form-select">
                <option value="pending">Pending</option>
                <option value="completed">Completed</option>
              </select>
            </div>
            <button class="btn btn-primary">{{ editing ? 'Update' : 'Create' }}</button>
            <button v-if="editing" @click="cancelEdit" class="btn btn-secondary ms-2">Cancel</button>
          </form>
        </div>
      </div>

      <!-- Task List -->
      <div v-if="tasks.length" class="row row-cols-1 row-cols-md-2 g-3">
        <div v-for="task in tasks" :key="task.id" class="col">
          <div class="card border-left border-4" :class="task.status === 'completed' ? 'border-success' : 'border-warning'">
            <div class="card-body">
              <h5 class="card-title">{{ task.title }}</h5>
              <p class="card-text">{{ task.description }}</p>
              <p class="text-muted">Due: {{ task.due_date }}</p>
              <span class="badge" :class="task.status === 'completed' ? 'bg-success' : 'bg-warning'">
                {{ task.status }}
              </span>
              <div class="mt-3">
                <button class="btn btn-sm btn-outline-primary me-2" @click="editTask(task)">Edit</button>
                <button class="btn btn-sm btn-outline-danger" @click="deleteTask(task.id)">Delete</button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <p v-else class="text-muted">No tasks found. Add one above!</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import api from '../services/api'
import { useRouter } from 'vue-router'

const router = useRouter()
const tasks = ref([])
const editing = ref(false)
const currentTaskId = ref(null)

const form = ref({
  title: '',
  description: '',
  due_date: '',
  status: 'pending',
})

// Fetch tasks on load
const fetchTasks = async () => {
  try {
    const res = await api.get('/tasks')
    tasks.value = res.data
  } catch (err) {
    console.error('Error fetching tasks:', err)
    router.push('/')
  }
}

onMounted(fetchTasks)

// Create new task
const createTask = async () => {
  await api.post('/tasks', form.value)
  resetForm()
  fetchTasks()
}

// Edit task
const editTask = (task) => {
  const { user_id, ...editableFields } = task
  form.value = { ...editableFields }
  editing.value = true
  currentTaskId.value = task.id
}


// Cancel editing
const cancelEdit = () => {
  resetForm()
  editing.value = false
}

// Update task
const updateTask = async () => {
  // Clone the form and exclude user_id
  const { user_id, ...cleanedForm } = form.value

  await api.put(`/tasks/${currentTaskId.value}`, cleanedForm)
  resetForm()
  editing.value = false
  fetchTasks()
}


// Delete task
const deleteTask = async (id) => {
  if (confirm('Are you sure you want to delete this task?')) {
    await api.delete(`/tasks/${id}`)
    fetchTasks()
  }
}

// Reset form
const resetForm = () => {
  form.value = {
    title: '',
    description: '',
    due_date: '',
    status: 'pending'
  }
}

// Logout
const logout = async () => {
  await api.post('/logout')
  localStorage.removeItem('token')
  router.push('/')
}
</script>

<style scoped>
.card.border-left {
  border-left-width: 5px !important;
}
</style>

