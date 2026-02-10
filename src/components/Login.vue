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
    <div class="login-card">
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
      
      <div class="toggle-link">Don't have an account? <a @click="emit('switch-to-register')">Register</a>
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

.login-card {
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
  font-size: 14px;
  margin-bottom: 15px;
  text-align: center;
  background-color: #ffe6e6;
  padding: 10px;
  border-radius: 5px;
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
  .login-card {
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

  .login-card {
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
}
</style>