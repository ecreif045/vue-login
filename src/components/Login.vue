<script setup>
import { ref } from 'vue'

const emit = defineEmits(['switch-to-register', 'login-success'])

const username = ref('')
const password = ref('')
const errorMessage = ref('')

const login = () => {
  errorMessage.value = ''
  
  const storedPassword = localStorage.getItem(username.value)
  
  if (!storedPassword) {
    errorMessage.value = 'Username does not exist. Please register first.'
    return
  }
  
  if (storedPassword !== password.value) {
    errorMessage.value = 'Incorrect password. Please try again.'
    return
  }
  
  localStorage.setItem('currentUser', username.value)
  emit('login-success', username.value)
}
</script>

<template>
  <div class="container">
    <h2>LOGIN</h2>
    <form @submit.prevent="login">
      <div class="form-group">
        <label>Username</label>
        <input 
          type="text" 
          v-model="username"
          placeholder="Enter username"
        >
      </div>
      
      <div class="form-group">
        <label>Password</label>
        <input 
          type="password" 
          v-model="password"
          placeholder="Enter password"
        >
      </div>
      
      <div class="error" v-if="errorMessage">{{ errorMessage }}</div>
      
      <button type="submit">Login</button>
    </form>
    
    <div class="toggle-link">
      Don't have an account? <a @click="emit('switch-to-register')">Register</a>
    </div>
  </div>
</template>

<style scoped>
.container {
  background: #d2daff;
  padding: 40px;
  border-radius: 10px;
}

h2 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  color: #555;
  font-weight: bold;
}

input {
  width: 100%;
  padding: 12px;
  border: 2px solid #ddd;
  border-radius: 5px;
  font-size: 14px;
  transition: border-color 0.3s;
}

input:focus {
  outline: none;
  border-color: #667eea;
}

.error {
  color: #e74c3c;
  font-size: 14px;
  margin-bottom: 15px;
  text-align: center;
}

.button-container {
  display: flex;
  justify-content: center;
  margin-top: 10px;
}

button {
  background-color: #009b55; 
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
  background-color: #257c59;
}

.toggle-link {
  text-align: center;
  margin-top: 20px;
  color: #666;
}

.toggle-link a {
  color: #667eea;
  text-decoration: none;
  font-weight: 600;
  cursor: pointer;
}

</style>
