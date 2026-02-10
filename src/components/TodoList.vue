<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const props = defineProps({
  username: {
    type: String,
    required: true
  }
})

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

watch(todos, () => {
  saveTodos()
}, { deep: true })

onMounted(() => {
  loadTodos()
})
</script>

<template>
  <div class="todo-page">
    <div class="container">
      <div class="user-header">Welcome, <strong>{{ username }}</strong></div>

      <h1 class="todo-title">To Do List</h1>
      
      <div class="input-group">
        <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new task" class="todo-input"/>
        <button @click="addTodo" class="add-btn">Add To Do</button>
      </div>

      <p class="tasks-left">[{{ remaining }} Tasks left]</p>

      <ul class="todo-list">
        <li v-for="todo in todos" :key="todo.id" class="todo-item">
          <input type="checkbox" v-model="todo.done" />
          <span :class="{ done: todo.done }">{{ todo.text }}</span>
          <button @click="removeTodo(todo.id)" class="remove-btn">Remove</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
.todo-page {
  width: 100%;
  min-height: 100%;
}

.container {
  background-color: #d2daff;
  color: #000000;
  font-family: Arial;
  border: 1px solid #000000;
  max-width: 800px;
  width: 100%;
  margin: 0 auto;
  padding: 40px;
  border-radius: 20px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.user-header {
  text-align: center;
  margin-bottom: 15px;
  color: #333;
  font-size: 16px;
}

.todo-title {
  text-align: center;
  background: #bbc8ff;
  text-decoration: underline;
  padding: 15px;
  border-radius: 5px;
  margin-bottom: 20px;
  font-size: 28px;
}

.input-group {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
  flex-wrap: wrap;
}

.todo-input {
  flex: 1;
  min-width: 200px;
  padding: 12px 16px;
  border: none;
  border-radius: 6px;
  font-size: 16px;
}

.add-btn {
  background-color: #009b55;
  color: white;
  padding: 12px 18px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
  white-space: nowrap;
}

.add-btn:hover {
  background-color: #257c59;
}

.tasks-left {
  text-align: center;
  margin: 15px 0;
  font-size: 16px;
  font-weight: bold;
}

.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.todo-item {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 15px;
  margin: 12px 0;
  padding: 15px;
  background-color: white;
  border-radius: 6px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.todo-item input[type="checkbox"] {
  justify-self: start;
  width: 20px;
  height: 20px;
  cursor: pointer;
}

.todo-item span {
  font-size: 16px;
  word-break: break-word;
  overflow-wrap: break-word;
  hyphens: auto;
  min-width: 0;
}

.done {
  text-decoration: line-through;
  color: gray;
}

.remove-btn {
  background-color: #e74c3c;
  padding: 8px 14px;
  font-size: 14px;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  flex-shrink: 0;
  white-space: nowrap;
}

.remove-btn:hover {
  background-color: #9e3125;
}

@media (max-width: 768px) {
  .container {
    max-width: 100%;
    padding: 30px 25px;
    border-radius: 10px;
  }

  .input-group {
    flex-direction: column;
  }

  .todo-input {
    width: 100%;
  }

  .add-btn {
    width: 100%;
  }
}

@media (max-width: 480px) {
  .container {
    padding: 25px 20px;
  }
  
  .todo-title {
    font-size: 22px;
    padding: 12px;
  }

  .user-header {
    font-size: 14px;
  }

  .todo-item {
    gap: 10px;
    padding: 12px;
  }

  .remove-btn {
    font-size: 12px;
    padding: 6px 12px;
  }
}
</style>