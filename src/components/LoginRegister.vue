<template>
  <div class="login-container">
    <div class="form-box">
      <div class="toggle-buttons">
        <button 
          :class="{ active: isLogin }" 
          @click="isLogin = true"
        >登录</button>
        <button 
          :class="{ active: !isLogin }" 
          @click="isLogin = false"
        >注册</button>
      </div>

      <!-- 登录表单 -->
      <form v-if="isLogin" @submit.prevent="handleLogin">
        <div class="form-group">
          <input 
            type="text" 
            v-model="loginForm.username" 
            placeholder="用户名"
            required
          >
        </div>
        <div class="form-group">
          <input 
            type="password" 
            v-model="loginForm.password" 
            placeholder="密码"
            required
          >
        </div>
        <button type="submit" class="submit-btn">登录</button>
      </form>

      <!-- 注册表单 -->
      <form v-else @submit.prevent="handleRegister">
        <div class="form-group">
          <input 
            type="text" 
            v-model="registerForm.username" 
            placeholder="用户名"
            required
          >
        </div>
        <div class="form-group">
          <input 
            type="email" 
            v-model="registerForm.email" 
            placeholder="邮箱地址"
            required
          >
        </div>
        <div class="form-group">
          <input 
            type="password" 
            v-model="registerForm.password" 
            placeholder="密码"
            required
          >
        </div>
        <div class="form-group">
          <input 
            type="password" 
            v-model="registerForm.confirmPassword" 
            placeholder="确认密码"
            required
          >
        </div>
        <button type="submit" class="submit-btn">注册</button>
      </form>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive } from 'vue'
import axios from 'axios'

// 修改API基础URL
const API_BASE_URL = 'http://localhost:8080'  // 改为正确的后端地址

// 控制显示登录还是注册表单
const isLogin = ref(true)

// 登录表单数据
const loginForm = reactive({
  username: '',
  password: ''
})

// 注册表单数据
const registerForm = reactive({
  username: '',
  password: '',
  confirmPassword: '',
  email: ''
})

// 登录处理
const handleLogin = async () => {
  try {
    const response = await axios.post(`${API_BASE_URL}/login`, {
      username: loginForm.username,
      password: loginForm.password
    }, {
      headers: {
        'Content-Type': 'application/json'
      }
    })
    
    console.log(response.data)
    if (response.data.success) {
      // 存储token
      localStorage.setItem('token', response.data.data.token)
      // 存储用户信息
      localStorage.setItem('user', JSON.stringify(response.data.data.user))
      alert(response.data.message) // "登录成功"
      // 这里可以添加路由跳转
    }
  } catch (error) {
    console.error('登录失败：', error)
    const errorMessage = error.response?.data?.message || '登录失败，请重试'
    alert(errorMessage)
  }
}

// 注册处理
const handleRegister = async () => {
  if (registerForm.password !== registerForm.confirmPassword) {
    alert('两次输入的密码不一致！')
    return
  }

  try {
    const response = await axios.post(`${API_BASE_URL}/register`, {
      username: registerForm.username,
      password: registerForm.password,
      email: registerForm.email
    }, {
      headers: {
        'Content-Type': 'application/json'
      }
    })
    
    if (response.data.success) {
      alert(response.data.message) // "注册成功"
      // 存储token和用户信息
      localStorage.setItem('token', response.data.data.token)
      localStorage.setItem('user', JSON.stringify(response.data.data.user))
      
      // 清空表单并切换到登录界面
      isLogin.value = true
      registerForm.username = ''
      registerForm.password = ''
      registerForm.confirmPassword = ''
      registerForm.email = ''
    }
  } catch (error) {
    console.error('注册失败：', error)
    const errorMessage = error.response?.data?.message || '注册失败，请重试'
    alert(errorMessage)
  }
}
</script>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background-color: #f5f5f5;
}

.form-box {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 400px;
}

.toggle-buttons {
  display: flex;
  margin-bottom: 2rem;
}

.toggle-buttons button {
  flex: 1;
  padding: 0.5rem;
  border: none;
  background: none;
  cursor: pointer;
  font-size: 1rem;
}

.toggle-buttons button.active {
  border-bottom: 2px solid #409eff;
  color: #409eff;
}

.form-group {
  margin-bottom: 1rem;
}

input {
  width: 100%;
  padding: 0.8rem;
  border: 1px solid #dcdfe6;
  border-radius: 4px;
  font-size: 1rem;
}

input:focus {
  outline: none;
  border-color: #409eff;
}

.submit-btn {
  width: 100%;
  padding: 0.8rem;
  background-color: #409eff;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1rem;
  cursor: pointer;
}

.submit-btn:hover {
  background-color: #66b1ff;
}
</style> 