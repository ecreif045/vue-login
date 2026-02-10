<script setup>
import { ref, onMounted } from 'vue'
import Register from './components/Register.vue'
import Login from './components/Login.vue'
import TodoList from './components/TodoList.vue'
import RewardTiers from './components/RewardTiers.vue'
import Sidebar from './components/Sidebar.vue'

const currentPage = ref('register')
const currentMainPage = ref('TodoList')
const loggedInUser = ref('')
const isSidebarOpen = ref(false)

const switchToLogin = () => {
  currentPage.value = 'login'
}

const switchToRegister = () => {
  currentPage.value = 'register'
}

const handleLoginSuccess = (username) => {
  loggedInUser.value = username
  currentPage.value = 'main'
  currentMainPage.value = 'TodoList'
  console.log('Login successful, switching to main app')
}

const handleNavigation = (page) => {
  currentMainPage.value = page
  if (window.innerWidth <= 768) {
    isSidebarOpen.value = false
  }
}

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value
}

const closeSidebar = () => {
  isSidebarOpen.value = false
}

const logout = () => {
  localStorage.removeItem('currentUser')
  loggedInUser.value = ''
  currentPage.value = 'login'
  currentMainPage.value = 'TodoList'
  isSidebarOpen.value = false
}

onMounted(() => {
  const currentUser = localStorage.getItem('currentUser')
  if (currentUser) {
    loggedInUser.value = currentUser
    currentPage.value = 'main'
    currentMainPage.value = 'TodoList'
    console.log('User already logged in, showing main app')
  }
})
</script>

<template>
  <div id="app">
    <Register v-if="currentPage === 'register'" @switch-to-login="switchToLogin"/>
    <Login v-if="currentPage === 'login'" @switch-to-register="switchToRegister" @login-success="handleLoginSuccess"/>
    
    <div v-if="currentPage === 'main'" class="main-layout">
      <button class="mobile-menu-btn" @click="toggleSidebar">
        <span class="hamburger-icon">☰</span>
      </button>

      <div v-if="isSidebarOpen" class="sidebar-overlay" @click="closeSidebar"></div>

      <div :class="['sidebar-wrapper', { 'sidebar-open': isSidebarOpen }]">
        <Sidebar 
          :username="loggedInUser" 
          :currentPage="currentMainPage"
          @navigate="handleNavigation"
          @logout="logout"/>
      </div>
      
      <main class="main-content">
        <TodoList v-if="currentMainPage === 'TodoList'" :username="loggedInUser"/>
        
        <RewardTiers v-if="currentMainPage === 'RewardTiers'" />
      </main>
    </div>
  </div>
</template>

<style>

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  height: 100%;
  width: 100%;
  overflow-x: hidden;
}

body {
  font-family: Arial, sans-serif;
  background: #3b4ea3;
}

#app {
  width: 100%;
  min-height: 100vh;
}

.main-layout {
  display: flex;
  min-height: 100vh;
  width: 100%;
  background: #ecf0f1;
  position: relative;
}

.mobile-menu-btn {
  display: none;
  position: fixed;
  top: 15px;
  left: 15px;
  z-index: 1001;
  background: #2c3e50;
  color: white;
  border: none;
  padding: 12px 16px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  transition: background-color 0.3s;
}

.mobile-menu-btn:hover {
  background: #34495e;
}

.hamburger-icon {
  display: block;
  line-height: 1;
}

.sidebar-wrapper {
  position: fixed;
  left: 0;
  top: 0;
  height: 100vh;
  width: 260px;
  z-index: 1000;
  transition: transform 0.3s ease;
}

.sidebar-overlay {
  display: none;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 999;
}

.main-content {
  flex: 1;
  margin-left: 260px;
  width: calc(100% - 260px);
  min-height: 100vh;
  padding: 20px;
  background: #ecf0f1;
  overflow-x: hidden;
}

@media (max-width: 1024px) {
  .main-content {
    padding: 20px 15px;
  }
}

@media (max-width: 768px) {
  .mobile-menu-btn {
    display: block;
  }

  .sidebar-wrapper {
    transform: translateX(-100%);
  }

  .sidebar-wrapper.sidebar-open {
    transform: translateX(0);
  }

  .main-content {
    margin-left: 0;
    width: 100%;
    padding: 70px 15px 15px;
  }
}

@media (max-width: 480px) {
  .sidebar-wrapper {
    width: 280px;
  }

  .main-content {
    padding: 65px 10px 10px;
  }

  .mobile-menu-btn {
    top: 10px;
    left: 10px;
    padding: 10px 14px;
    font-size: 18px;
  }
}

</style>