<script setup>
import { ref } from 'vue'

const emit = defineEmits(['switch-to-login'])

const username = ref('')
const password = ref('')
const usernameError = ref('')
const passwordError = ref('')
const successMessage = ref('')

const register = () => {
  usernameError.value = ''
  passwordError.value = ''
  successMessage.value = ''
  
  if (username.value.length < 10) {
    usernameError.value = 'Username must be at least 10 characters'
    return
  }
  
  if (password.value.length < 10) {
    passwordError.value = 'Password must be at least 10 characters'
    return
  }
  
  const existingUser = localStorage.getItem(username.value)
  if (existingUser) {
    usernameError.value = 'Username already exists'
    return
  }
  
  localStorage.setItem(username.value, password.value)
  successMessage.value = 'Registration successful! Redirecting to login...'
  
  username.value = ''
  password.value = ''
  
  setTimeout(() => {
    emit('switch-to-login')
  }, 2000)
}
</script>

<template>
  <div class="container">

    <h2>REGISTER</h2>
    <form @submit.prevent="register">

      <div class="form-group">
        <label>Username</label>
        <input type="text" v-model="username" placeholder="Enter username (min 10 characters)">
        <div class="error" v-if="usernameError">{{ usernameError }}</div>
      </div>
      
      <div class="form-group">
        <label>Password</label>
        <input type="password" v-model="password" placeholder="Enter password (min 10 characters)">
        <div class="error" v-if="passwordError">{{ passwordError }}</div>
      </div>
      
      <div class="success" v-if="successMessage">{{ successMessage }}</div>
      
      <button type="submit">Register</button>
    </form>
    
    <div class="toggle-link">Already have an account? 
      <a @click="emit('switch-to-login')">Login</a>
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
  font-size: 12px;
  margin-top: 5px;
}

.success {
  color: #27ae60;
  font-size: 14px;
  margin-bottom: 15px;
  text-align: center;
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
