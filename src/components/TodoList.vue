<template>
  <div>
    <form @submit.prevent="addTodo" class="form">
      <input v-model="newTodo" placeholder="Tambah kegiatan..." />
      <button type="submit">Tambah</button>
    </form>

    <div class="filter">
      <label>
        <input type="checkbox" v-model="showUnfinishedOnly" />
        Tampilkan hanya yang belum selesai
      </label>
    </div>

    <ul class="todo-list">
      <li v-for="(todo, index) in filteredTodos" :key="index" :class="{ done: todo.done }">
        <input type="checkbox" v-model="todo.done" @change="saveTodos" />
        <span>{{ todo.text }}</span>
        <button @click="removeTodo(index)">❌</button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted } from 'vue'

const newTodo = ref('')
const todos = ref([])
const showUnfinishedOnly = ref(false)

// Simpan ke localStorage setiap kali todos berubah
watch(todos, () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}, { deep: true })

// Ambil data dari localStorage saat komponen dimuat
onMounted(() => {
  const saved = localStorage.getItem('todos')
  if (saved) {
    todos.value = JSON.parse(saved)
  }
})

const addTodo = () => {
  if (newTodo.value.trim() !== '') {
    todos.value.push({ text: newTodo.value.trim(), done: false })
    newTodo.value = ''
  }
}

const removeTodo = (index) => {
  todos.value.splice(index, 1)
}

const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

const filteredTodos = computed(() => {
  return showUnfinishedOnly.value
    ? todos.value.filter(todo => !todo.done)
    : todos.value
})
</script>

<style scoped>
.form {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}
input[type="text"] {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 2px solid #ccc;
  border-radius: 8px;
}
button {
  padding: 10px 15px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}
button:hover {
  background-color: #45a049;
}
.filter {
  margin-bottom: 20px;
  text-align: left;
}
.todo-list {
  list-style: none;
  padding: 0;
}
.todo-list li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #f9f9f9;
  padding: 10px 15px;
  margin-bottom: 10px;
  border-radius: 8px;
  transition: background 0.3s;
}
.todo-list li:hover {
  background: #f0f0f0;
}
.todo-list li.done span {
  text-decoration: line-through;
  color: #999;
}
.todo-list li span {
  flex: 1;
  text-align: left;
  margin-left: 10px;
}
.todo-list li button {
  background: transparent;
  border: none;
  font-size: 18px;
  color: red;
  cursor: pointer;
}
</style>
