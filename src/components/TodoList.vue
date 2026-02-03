<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const props = defineProps({
  username: {
    type: String,
    required: true
  }
})

const emit = defineEmits(['logout'])
const newTodo = ref('')
const todos = ref([])

function addTodo() {
  if (newTodo.value.trim() === '') return
  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    done: false
  })
  newTodo.value = ''
}

function removeTodo(id) {
  todos.value = todos.value.filter(todo => todo.id !== id)
}

const remaining = computed(() => {
  return todos.value.filter(todo => !todo.done).length
})

function saveTodos() {
  localStorage.setItem(`todos_${props.username}`, JSON.stringify(todos.value))
}

function loadTodos() {
  const savedTodos = localStorage.getItem(`todos_${props.username}`)
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos)
  }
}

function handleLogout() {
  saveTodos() 
  emit('logout')
}

watch(todos, () => {
  saveTodos()
}, { deep: true })

onMounted(() => {
  loadTodos()
})
</script>

<template>

  <div class="container">

    <div class="user-header">Welcome, <strong>{{ username }}</strong></div>

    <h1 class="todo-title">To Do List</h1>
    <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new task" class="todo-input"/>
    <button @click="addTodo" class="add-btn">Add To Do</button>

    <p>[{{ remaining }} Tasks left]</p>

    <ul class="todo-list">
      <li v-for="todo in todos" :key="todo.id" class="todo-item">
        <input type="checkbox" v-model="todo.done" />
        <span :class="{ done: todo.done }">{{ todo.text }}</span>
        <button @click="removeTodo(todo.id)" class="remove-btn">Remove</button>
      </li>
    </ul>

    <button @click="handleLogout" class="logout-btn">Logout</button>
  </div>

</template>

<style scoped>

.container {
  background-color: #d2daff;
   color: #000000;
  font-family: Arial;
  border: 1px solid #000000;
  max-width: 400px;
  margin: 40px auto;
  padding: 30px;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.user-header {
  text-align: center;
  margin-bottom: 15px;
  color: #333;
  font-size: 14px;
}

.todo-title {
  text-align: center;
  background: #bbc8ff;
  text-decoration: underline;
  padding: 10px;
  border-radius: 5px;
  margin-bottom: 20px;
}

 .todo-input {
  width: calc(100% - 110px);
  padding: 8px 14px;
  border: none;
  border-radius: 6px;
  margin-bottom: 10px;
  margin-right: 5px;
}

.add-btn {
  background-color: #009b55;
  color: white;
  padding: 8px 14px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  width: auto;
}

.add-btn:hover {
  background-color: #257c59;
}

button {
  background-color: #009b55;
  color: white;
  padding: 8px 14px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
}

button:hover {
  background-color: #257c59;
}

p {
  text-align: center;
  margin: 15px 0;
}

.todo-list {
  list-style: none;
  padding: 0;
}

.todo-item {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 10px;
  margin: 10px 0;
  padding: 10px;
  background-color: white;
  border-radius: 4px;
}

.todo-item input[type="checkbox"] {
  justify-self: start;
  width: auto;
  cursor: pointer;
}

.done {
  text-decoration: line-through;
  color: gray;
}

.remove-btn {
  background-color: #e74c3c;
  padding: 6px 12px;
  font-size: 14px;
}

.remove-btn:hover {
  background-color: #9e3125;
}

.logout-btn {
  width: 100%;
  background: #e74c3c;
  color: white;
  padding: 8px 14px;
  border: none;
  border-radius: 6px;
  display: block;
  margin: 10px auto;
  width: 200px;
  font-size: 17px;
}

button:hover {
  background-color: #9e3125;
}

</style>
