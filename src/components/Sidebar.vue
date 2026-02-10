<script setup>
import { ref } from 'vue'

const props = defineProps({
  username: {
    type: String,
    required: true
  },
  currentPage: {
    type: String,
    default: 'TodoList'
  }
})

const emit = defineEmits(['navigate', 'logout'])

const navigate = (page) => {
  emit('navigate', page)
}
</script>

<template>
  <div>
    <aside :class="['sidebar', { 'sidebar-open': isOpen }]">
      <div class="user-info">
        <div class="avatar">{{ username.charAt(0).toUpperCase() }}</div>
        <div class="user-details">
          <h3>{{ username }}</h3>
          <p>Welcome back!</p>
        </div>
      </div>

      <nav class="nav-menu">
        <button :class="['nav-item', { active: currentPage === 'TodoList' }]"
          @click="navigate('TodoList')">
          <span class="label">To-Do List</span>
        </button>

        <button :class="['nav-item', { active: currentPage === 'RewardTiers' }]"
          @click="navigate('RewardTiers')">
          <span class="label">Reward Tiers</span>
        </button>
      </nav>

      <button class="logout-btn" @click="emit('logout')">
        <span class="label">Logout</span>
      </button>
    </aside>
  </div>
</template>

<style scoped>

.sidebar {
  position: fixed;
  left: 0;
  top: 0;
  height: 100vh;
  width: 260px;
  background: #2c3e50;
  color: white;
  padding: 20px;
  display: flex;
  flex-direction: column;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
  z-index: 1000;
}

.user-info {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 20px 10px;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  margin-bottom: 20px;
}

.avatar {
  width: 50px;
  height: 50px;
  background: #3b82f6;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  font-weight: bold;
}

.user-details h3 {
  margin: 0;
  font-size: 16px;
  font-weight: 600;
}

.user-details p {
  margin: 0;
  font-size: 12px;
  color: #bbb;
}

.nav-menu {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 12px 15px;
  background: transparent;
  border: none;
  color: #bbb;
  cursor: pointer;
  border-radius: 8px;
  transition: all 0.3s;
  font-size: 15px;
  text-align: left;
  width: 100%;
}

.nav-item:hover {
  background: rgba(255, 255, 255, 0.1);
  color: white;
}

.nav-item.active {
  background: #3b82f6;
  color: white;
}

.logout-btn {
  display: flex;
  align-items: center;
  gap: 15px;
  padding: 12px 15px;
  background: #e74c3c;
  border: none;
  color: white;
  cursor: pointer;
  border-radius: 8px;
  transition: all 0.3s;
  font-size: 15px;
  margin-top: auto;
  width: 100%;
}

.logout-btn:hover {
  background: #c0392b;
}
</style>