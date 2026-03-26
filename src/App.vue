<script setup>
import { ref, onMounted } from 'vue'
import Register from './components/Register.vue'
import Login from './components/Login.vue'
import TodoList from './components/TodoList.vue'
import RewardTiers from './components/RewardTiers.vue'
import Sidebar from './components/Sidebar.vue'
import DataExplorer from './components/Data.vue'
import PhotoGallery from './components/PhotoGallery.vue'   // ← NEW

const currentPage = ref('register')
const currentMainPage = ref('TodoList')
const loggedInUser = ref('')
const isSidebarOpen = ref(false)

const switchToLogin = () => { currentPage.value = 'login' }
const switchToRegister = () => { currentPage.value = 'register' }

const handleLoginSuccess = (username) => {
  loggedInUser.value = username
  currentPage.value = 'main'
  currentMainPage.value = 'TodoList'
}

const handleNavigation = (page) => {
  currentMainPage.value = page
  if (window.innerWidth <= 768) isSidebarOpen.value = false
}

const toggleSidebar = () => { isSidebarOpen.value = !isSidebarOpen.value }
const closeSidebar  = () => { isSidebarOpen.value = false }

const logout = () => {
  localStorage.removeItem('currentUser')
  loggedInUser.value = ''
  currentPage.value = 'login'
  currentMainPage.value = 'TodoList'
  isSidebarOpen.value = false
}

onMounted(() => {
  const user = localStorage.getItem('currentUser')
  if (user) {
    loggedInUser.value = user
    currentPage.value = 'main'
  }
})
</script>

<template>
  <div id="app">
    <Register v-if="currentPage === 'register'" @switch-to-login="switchToLogin"/>
    <Login    v-if="currentPage === 'login'"    @switch-to-register="switchToRegister" @login-success="handleLoginSuccess"/>

    <div v-if="currentPage === 'main'" class="main-layout">
      <button class="mobile-menu-btn" @click="toggleSidebar">☰</button>
      <div v-if="isSidebarOpen" class="sidebar-overlay" @click="closeSidebar"></div>

      <div :class="['sidebar-wrapper', { 'sidebar-open': isSidebarOpen }]">
        <Sidebar
          :username="loggedInUser"
          :currentPage="currentMainPage"
          @navigate="handleNavigation"
          @logout="logout"
        />
      </div>

      <main class="main-content">
        <TodoList     v-if="currentMainPage === 'TodoList'"     :username="loggedInUser"/>
        <RewardTiers  v-if="currentMainPage === 'RewardTiers'"/>
        <DataExplorer v-if="currentMainPage === 'DataExplorer'"/>
        <PhotoGallery v-if="currentMainPage === 'PhotoGallery'"/>  <!-- ← NEW -->
      </main>
    </div>
  </div>
</template>

<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
html, body { height: 100%; width: 100%; overflow-x: hidden; background: #3b4ea3; }
body { font-family: Arial, sans-serif; }
#app { width: 100%; min-height: 100vh; }

.main-layout { display: flex; min-height: 100vh; width: 100%; background: #ecf0f1; position: relative; }

.mobile-menu-btn { display: none; position: fixed; top: 15px; left: 15px; z-index: 1001; background: #2c3e50; color: white; border: none; padding: 12px 16px; border-radius: 8px; cursor: pointer; font-size: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
.mobile-menu-btn:hover { background: #34495e; }

.sidebar-wrapper { position: fixed; left: 0; top: 0; height: 100vh; width: 260px; z-index: 1000; transition: transform 0.3s ease; }
.sidebar-overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 999; }

.main-content { flex: 1; margin-left: 260px; width: calc(100% - 260px); min-height: 100vh; padding: 20px; background: #ecf0f1; overflow-x: hidden; }

@media (max-width: 768px) {
  .mobile-menu-btn { display: block; }
  .sidebar-wrapper { transform: translateX(-100%); }
  .sidebar-wrapper.sidebar-open { transform: translateX(0); }
  .sidebar-overlay { display: block; }
  .main-content { margin-left: 0; width: 100%; padding: 70px 15px 15px; }
}
</style>