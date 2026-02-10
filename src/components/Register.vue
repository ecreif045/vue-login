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
    <div class="register-card">
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
  </div>
</template>

<style scoped>

.container {
  min-height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  box-sizing: border-box;
  background: #3b4ea3;
}

.register-card {
  background: #d2daff;
  padding: 40px;
  border-radius: 10px;
  width: 100%;
  max-width: 450px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
}

h2 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
  font-size: 28px;
}

.form-group {
  margin-bottom: 20px;
}

label {
  display: block;
  margin-bottom: 8px;
  color: #555;
  font-weight: bold;
  font-size: 16px;
}

input {
  width: 100%;
  padding: 14px;
  border: 2px solid #ddd;
  border-radius: 5px;
  font-size: 16px;
  transition: border-color 0.3s;
  box-sizing: border-box;
}

input:focus {
  outline: none;
  border-color: #667eea;
}

.error {
  color: #e74c3c;
  font-size: 13px;
  margin-top: 8px;
  background-color: #ffe6e6;
  padding: 8px 10px;
  border-radius: 4px;
  border-left: 3px solid #e74c3c;
}

.success {
  color: #27ae60;
  font-size: 15px;
  margin-bottom: 15px;
  text-align: center;
  background-color: #d4edda;
  padding: 12px;
  border-radius: 5px;
  border-left: 3px solid #27ae60;
  font-weight: 500;
}

button {
  background-color: #009b55; 
  color: white;
  padding: 14px 20px;
  border: none;
  border-radius: 6px;
  display: block;
  margin: 10px auto;
  width: 100%;
  max-width: 250px;
  font-size: 18px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #257c59;
}

.toggle-link {
  text-align: center;
  margin-top: 25px;
  color: #666;
  font-size: 15px;
}

.toggle-link a {
  color: #667eea;
  text-decoration: none;
  font-weight: 600;
  cursor: pointer;
  transition: color 0.3s;
}

.toggle-link a:hover {
  color: #4c5fd5;
  text-decoration: underline;
}

@media (min-width: 768px) {
  .register-card {
    padding: 50px;
  }

  h2 {
    font-size: 32px;
    margin-bottom: 35px;
  }
}

@media (max-width: 480px) {
  .container {
    padding: 15px;
  }

  .register-card {
    padding: 30px 25px;
  }

  h2 {
    font-size: 24px;
    margin-bottom: 25px;
  }

  label {
    font-size: 15px;
  }

  input {
    padding: 12px;
    font-size: 15px;
  }

  button {
    font-size: 16px;
    padding: 12px 18px;
  }

  .toggle-link {
    font-size: 14px;
  }

  .error {
    font-size: 12px;
  }

  .success {
    font-size: 14px;
  }
}
</style>