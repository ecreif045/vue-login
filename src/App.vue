<script setup>
import { ref, onMounted } from 'vue'
import Register from './components/Register.vue'
import Login from './components/Login.vue'
import TodoList from './components/TodoList.vue'

const currentPage = ref('register')
const loggedInUser = ref('')

const switchToLogin = () => {
  currentPage.value = 'login'
}

const switchToRegister = () => {
  currentPage.value = 'register'
}

const handleLoginSuccess = (username) => {
  loggedInUser.value = username
  currentPage.value = 'TodoList'
  console.log('Login successful, switching to To-do List')
}

const logout = () => {
  localStorage.removeItem('currentUser')
  loggedInUser.value = ''
  currentPage.value = 'login'
}

onMounted(() => {
  const currentUser = localStorage.getItem('currentUser')
  if (currentUser) {
    loggedInUser.value = currentUser
    currentPage.value = 'TodoList'
    console.log('User already logged in, showing list')
  }
})
</script>

<template>
  <div id="app">
    <Register v-if="currentPage === 'register'" @switch-to-login="switchToLogin"/>
    
    <Login v-if="currentPage === 'login'" @switch-to-register="switchToRegister"@login-success="handleLoginSuccess"/>
    
    <TodoList v-if="currentPage === 'TodoList'" :username="loggedInUser"@logout="logout"/>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: Arial, sans-serif;
  background: #3b4ea3;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

#app {
  width: 100%;
  max-width: 500px;
  padding: 20px;
}
</style>