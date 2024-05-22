<template>
  <div>
    <header class="header">
      <div class="button-container">
        <button @click="selectedMenu = 'Todos'">Todos</button>
        <button @click="selectedMenu = 'Posts'">Posts</button>
      </div>
    </header>

    <main>
      <div v-if="selectedMenu === 'Todos'" class="activity-container">
        <div class="container">
          <h1 class="title">Daftar Kegiatan</h1>
          <div class="input-container">
            <input v-model="input_content" @keyup.enter="addTodo" placeholder="Nama Kegiatan" />
            <button @click="addTodo">Tambahkan Kegiatan</button>
          </div>
          <ul class="activities-list">
            <li v-for="(todo, index) in filteredTodos" :key="index" class="activity-item">
              <span v-if="todo.done">✅</span>
              <span :class="{ completed: todo.done }">{{ todo.content }}</span>
              <div class="buttons">
                <button @click="todo.done = !todo.done" class="action-button">
                  {{ todo.done ? 'Uncheck' : 'Checklist' }}
                </button>
                <button @click="removeTodo(todo)" class="action-button">Hapus</button>
              </div>
            </li>
          </ul>
          <button @click="filterCompleted = !filterCompleted" class="filter-button">
            {{ filterCompleted ? 'Tampilkan Semua Kegiatan' : 'Tampilkan Kegiatan Yang Belum Selesai' }}
          </button>
        </div>
      </div>

      <div v-if="selectedMenu === 'Posts'" class="post-container">
        <div class="container">
          <h1 class="title">Daftar Posts</h1>
          <label for="user-select">Filter by User:</label>
          <select id="user-select" v-model="selectedUser">
            <option value="">All Users</option>
            <option v-for="user in users" :key="user.id" :value="user.id">
              {{ user.name }}
            </option>
          </select>
          <ul class="posts-list">
            <li v-for="post in filteredPosts" :key="post.id" class="post-item">
              <h2>{{ post.title }}</h2>
              <p>{{ post.body }}</p>
            </li>
          </ul>
        </div>
      </div>
    </main>

    <div v-if="loading" class="loading">
      <div class="icons">
        <i class="ri-arrow-left-s-line"></i>
        <i class="ri-arrow-right-s-line"></i>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'

const selectedMenu = ref('Todos')
const selectedUser = ref(null)
const users = ref([])
const posts = ref([])
const loading = ref(true) // Menandai saat loading sedang berlangsung
const name = ref('')
const input_content = ref('')
const filterCompleted = ref(false)

const todos = ref([])

const todos_asc = computed(() => todos.value.sort((a, b) => b.createdAt - a.createdAt))
const filteredTodos = computed(() => filterCompleted.value ? todos_asc.value.filter(todo => !todo.done) : todos_asc.value)

const addTodo = () => {
  if (input_content.value.trim() === '') return
  todos.value.push({ content: input_content.value, done: false, createdAt: new Date().getTime() })
  input_content.value = ''
}

const removeTodo = todo => {
  const index = todos.value.findIndex(t => t === todo)
  if (index !== -1) todos.value.splice(index, 1)
}

const filteredPosts = computed(() => {
  if (!selectedUser.value) return posts.value
  return posts.value.filter(post => post.userId === selectedUser.value)
})

onMounted(async () => {
  // Fetch users and posts data from the API
  const usersResponse = await fetch('https://jsonplaceholder.typicode.com/users')
  const postsResponse = await fetch('https://jsonplaceholder.typicode.com/posts')
  users.value = await usersResponse.json()
  posts.value = await postsResponse.json()
  loading.value = false // Menandai loading selesai setelah data terambil
})
</script>

<style scoped>
.header {
  display: flex;
  justify-content: flex-start;
  background-color: #745d5c;
  padding: 10px 20px;
}

.button-container {
  display: flex;
  gap: 10px;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 80vh;
  font-family: Arial, sans-serif;
  background-image: url('https://img.freepik.com/free-vector/abstract-geometric-pattern_1319-110.jpg?t=st=1714299332~exp=1714302932~hmac=6e9993d89a8bcd867f6788f1c11869150dd34d4723d9dc934d0c47e62349a3e0&w=740');
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
}

.title {
  font-size: 24px;
  color: #803D3B;
  margin-bottom: 20px;
}

.input-container {
  display: flex;
  margin-bottom: 20px;
}

.input-container input {
  flex: 1;
  padding: 10px;
  border: 1px solid #803D3B;
  border-radius: 5px;
}

.input-container button {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  background-color: #9f6462;
  color: rgb(255, 255, 255);
  cursor: pointer;
}

.activities-list, .posts-list {
  list-style: none;
  padding: 0;
}

.activity-item, .post-item {
  display: flex;
  flex-direction: column;
  padding: 10px;
  border-bottom: 1px solid #ffffff;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 5px;
  margin-bottom: 10px;
}

.activity-item span, .post-item h2, .post-item p {
  flex: 1;
  font-size: 16px;
}

.buttons {
  display: flex;
  justify-content: space-between;
}

.action-button, .fetch-button {
  padding: 5px 10px;
  margin-left: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background-color: #803D3B;
  color: #ffffff;
}

.completed {
  text-decoration: line-through;
  color: #803D3B;
}

.filter-button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #803D3B;
  color: white;
  cursor: pointer;
  margin-top: 20px;
}

.button-container {
  text-align: center;
  margin-top: 30px;
}

.button-container button {
  padding: 10px 20px;
  background-color: #803D3B;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-right: 10px;
}

.button-container button:last-child {
  margin-right: 0;
}

.button-container button:hover {
  background-color: #803D3B;
}

.loading {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 9999;
}

.icons {
  display: flex;
  position: relative;
}

.loading i {
  font-size: 3em;
  color: transparent;
  background: linear-gradient(45deg, #f6f6f6, #dddddd);
  background-clip: text;
  -webkit-background-clip: text;
  animation: up 1s ease infinite;
}

@keyframes up {
  0%, 100% {
    transform: scale(1);
    opacity: 0.6;
  }

  50% {
    transform: scale(1.5);
    opacity: 1;
  }
}

.loading i:nth-child(2) {
  margin-left: -14px;
  animation: up2 1s ease infinite;
}

@keyframes up2 {
  0%, 100% {
    transform: scale(1.5);
    opacity: 1;
  }

  50% {
    transform: scale(1);
    opacity: 0.6;
  }
}

@media (max-width: 768px) {
  .container {
    width: 300px;
  }

  .input-container input {
    width: calc(100% - 75px);
  }
}
</style>