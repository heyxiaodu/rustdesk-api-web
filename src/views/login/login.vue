<template>
  <div class="login-page">
    <!-- ========== 左侧品牌面板 ========== -->
    <div class="brand-panel">
      <div class="brand-inner">
        <!-- Logo -->
        <img src="@/assets/logo.png" alt="logo" class="brand-logo"/>

        <!-- 标题 -->
        <h1 class="brand-title">New here?</h1>

        <!-- 副标题 -->
        <p class="brand-subtitle">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit.<br/>
          Join our platform to manage your remote connections seamlessly.
        </p>

        <!-- 注册按钮 -->
        <el-button v-if="allowRegister" class="btn-register" @click="register">
          注册
        </el-button>

        <!-- 装饰弧线（CSS 实现） -->
        <div class="arc-decoration arc-top-left"></div>
        <div class="arc-decoration arc-bottom-right"></div>
      </div>
    </div>

    <!-- ========== 右侧登录表单面板 ========== -->
    <div class="form-panel">
      <div class="form-inner">
        <h2 class="form-title">登录</h2>

        <el-form v-if="!disablePwd" label-position="top" class="login-form">
          <!-- 用户名 -->
          <el-form-item :label="T('Username')">
            <el-input
              v-model="form.username"
              type="username"
              class="login-input"
              placeholder="用户名"
            >
              <template #prefix>
                <el-icon class="input-icon"><User /></el-icon>
              </template>
            </el-input>
          </el-form-item>

          <!-- 密码 -->
          <el-form-item :label="T('Password')">
            <el-input
              v-model="form.password"
              type="password"
              @keyup.enter.native="login"
              show-password
              class="login-input"
              placeholder="密码"
            >
              <template #prefix>
                <el-icon class="input-icon"><Lock /></el-icon>
              </template>
            </el-input>
          </el-form-item>

          <!-- 验证码 -->
          <el-form-item :label="T('Captcha')" v-if="captchaCode">
            <el-input
              v-model="form.captcha"
              @keyup.enter.native="login"
              class="login-input captcha-input"
            >
              <template #prefix>
                <el-icon class="input-icon"><CircleCheck /></el-icon>
              </template>
              <template #append>
                <img :src="captchaCode.b64" @click="loadCaptcha" class="captcha" alt="captcha"/>
              </template>
            </el-input>
          </el-form-item>

          <!-- 登录按钮 -->
          <el-button @click="login" type="primary" class="btn-login">
            LOGIN
          </el-button>
        </el-form>

        <!-- 第三方登录区域 -->
        <div class="social-login">
          <p class="social-label">或者使用第三方账号登录</p>
          <div class="social-icons">
            <el-button class="social-btn" @click="handleOIDCLogin('google')" title="Google">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="none"><path d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z" fill="#4285F4"/><path d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z" fill="#34A853"/><path d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z" fill="#FBBC05"/><path d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z" fill="#EA4335"/></svg>
            </el-button>
            <el-button class="social-btn" @click="handleOIDCLogin('github')" title="GitHub">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="#1D2129"><path d="M12 0C5.374 0 0 5.373 0 12c0 5.302 3.438 9.8 8.207 11.387.599.111.793-.261.793-.577v-2.234c-3.338.726-4.033-1.416-4.033-1.416-.546-1.387-1.333-1.756-1.333-1.756-1.089-.745.083-.729.083-.729 1.205.084 1.839 1.237 1.839 1.237 1.07 1.834 2.807 1.304 3.492.997.107-.775.418-1.305.762-1.604-2.665-.305-5.467-1.334-5.467-5.931 0-1.311.469-2.381 1.236-3.221-.124-.303-.535-1.524.117-3.176 0 0 1.008-.322 3.301 1.23A11.509 11.509 0 0112 5.803c1.02.005 2.047.138 3.006.404 2.291-1.552 3.297-1.23 3.297-1.23.653 1.653.242 2.874.118 3.176.77.84 1.235 1.911 1.235 3.221 0 4.609-2.807 5.624-5.479 5.921.43.372.823 1.102.823 2.222v3.293c0 .319.192.694.801.576C20.566 21.797 24 17.3 24 12c0-6.627-5.373-12-12-12z"/></svg>
            </el-button>
            <el-button class="social-btn" @click="handleOIDCLogin('facebook')" title="Facebook">
              <svg width="20" height="20" viewBox="0 0 24 24" fill="#1877F2"><path d="M24 12.073c0-6.627-5.373-12-12-12s-12 5.373-12 12c0 5.99 4.388 10.954 10.125 11.854v-8.385H7.078v-3.47h3.047V9.43c0-3.007 1.792-4.669 4.533-4.669 1.312 0 2.686.235 2.686.235v2.953H15.83c-1.491 0-1.956.925-1.956 1.874v2.25h3.328l-.532 3.47h-2.796v8.385C19.612 23.027 24 18.062 24 12.073z"/></svg>
            </el-button>
            <el-button class="social-btn" @click="handleOIDCLogin('twitter')" title="Twitter/X">
              <svg width="18" height="18" viewBox="0 0 24 24" fill="#1D2129"><path d="M18.244 2.25h3.308l-7.227 8.26 8.502 11.24H16.17l-5.214-6.817L4.99 21.75H1.68l7.73-8.835L1.254 2.25H8.08l4.713 6.231zm-1.161 17.52h1.833L7.084 4.126H5.117z"/></svg>
            </el-button>
          </div>
        </div>

        <!-- OIDC 快捷入口 -->
        <div class="oidc-divider" v-if="options.length > 0">
          <el-button
            v-for="(option, index) in options"
            :key="index"
            class="oidc-quick-btn"
            @click="handleOIDCLogin(option.name)"
          >
            <img :src="getProviderImage(option.name)" alt="" class="oidc-quick-icon"/>
            {{ T(option.name) }}
          </el-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { reactive, onMounted, ref } from 'vue'
  import { useUserStore } from '@/store/user'
  import { ElMessage } from 'element-plus'
  import { T } from '@/utils/i18n'
  import { useRoute, useRouter } from 'vue-router'
  import { loginOptions, captcha } from '@/api/login'
  import { getCode, removeCode } from '@/utils/auth'
  import { User, Lock, CircleCheck } from '@element-plus/icons-vue'

  const userStore = useUserStore()
  const route = useRoute()
  const router = useRouter()
  const options = reactive([])

  let platform = window.navigator.platform
  if (navigator.platform.indexOf('Mac') === 0) platform = 'mac'
  else if (navigator.platform.indexOf('Win') === 0) platform = 'windows'
  else if (navigator.platform.indexOf('Linux armv') === 0) platform = 'android'
  else if (navigator.platform.indexOf('Linux') === 0) platform = 'linux'

  const userAgent = navigator.userAgent
  let browser = 'Unknown Browser'
  if (/chrome|crios/i.test(userAgent)) browser = 'Chrome'
  else if (/firefox|fxios/i.test(userAgent)) browser = 'Firefox'
  else if (/safari/i.test(userAgent) && !/chrome/i.test(userAgent)) browser = 'Safari'
  else if (/edg/i.test(userAgent)) browser = 'Edge'

  const form = reactive({
    username: '',
    password: '',
    platform: platform,
    captcha: '',
    captcha_id: ''
  })

  const captchaCode = ref('')
  const redirect = route.query?.redirect

  const login = async () => {
    const res = await userStore.login(form).catch(e => e)
    if (!res.code) {
      ElMessage.success(T('LoginSuccess'))
      router.push({ path: redirect || '/', replace: true })
      return
    }
    if (res.code === 110) loadCaptcha()
  }

  const loadCaptcha = async () => {
    const r = await captcha().catch(_ => false)
    if (r) {
      captchaCode.value = r.data.captcha
      form.captcha_id = r.data.captcha.id
    }
  }

  const handleOIDCLogin = (provider) => {
    userStore.oidc(provider, platform, browser)
  }

  import googleImage from '@/assets/google.png'
  import githubImage from '@/assets/github.png'
  import oidcImage from '@/assets/oidc.png'
  import defaultImage from '@/assets/oidc.png'

  const providerImageMap = { google: googleImage, github: githubImage, oidc: oidcImage, default: defaultImage }
  const getProviderImage = (p) => providerImageMap[p?.toLowerCase()] || defaultImage

  const allowRegister = ref(false)
  const disablePwd = ref(false)

  const loadLoginOptions = async () => {
    try {
      const res = await loginOptions().catch(_ => false)
      if (!res?.data) return
      res.data.ops.forEach(o => options.push({ name: o }))
      if (res.data.auto_oidc) handleOIDCLogin(res.data.ops[0])
      disablePwd.value = res.data.disable_pwd
      allowRegister.value = res.data.register
      if (res.data.need_captcha) loadCaptcha()
    } catch (e) { console.error(e) }
  }

  onMounted(async () => {
    const code = getCode()
    if (code) {
      const res = await userStore.query(code)
      if (res) {
        removeCode()
        ElMessage.success(T('LoginSuccess'))
        router.push({ path: redirect || '/', replace: true })
      }
    } else {
      loadLoginOptions()
    }
  })

  const register = () => router.push('/register')
</script>

<style scoped lang="scss">
// ============================================================
// 左右分栏 · 现代通透登录页
// ============================================================

.login-page {
  display: flex;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

// ========== 左侧品牌面板 (60%) ==========
.brand-panel {
  flex: 0 0 60%;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  // 蓝色渐变背景
  background: linear-gradient(135deg, #4F8EFF 0%, #1E5EFF 40%, #0A2E8C 100%);
  overflow: hidden;

  // 右上角大弧线装饰
  &::before {
    content: '';
    position: absolute;
    top: -120px;
    right: -120px;
    width: 500px;
    height: 500px;
    border-radius: 50%;
    border: 80px solid rgba(255, 255, 255, 0.06);
    pointer-events: none;
  }

  // 左下角小弧线装饰
  &::after {
    content: '';
    position: absolute;
    bottom: -80px;
    left: -80px;
    width: 300px;
    height: 300px;
    border-radius: 50%;
    border: 60px solid rgba(255, 255, 255, 0.05);
    pointer-events: none;
  }
}

.brand-inner {
  position: relative;
  z-index: 1;
  text-align: center;
  max-width: 400px;
  padding: 40px;
}

.brand-logo {
  width: 80px;
  height: 80px;
  border-radius: 20px;
  margin-bottom: 40px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.25);
}

.brand-title {
  font-size: 42px;
  font-weight: 700;
  color: #ffffff;
  margin: 0 0 20px;
  letter-spacing: -0.5px;
}

.brand-subtitle {
  font-size: 15px;
  line-height: 1.7;
  color: rgba(255, 255, 255, 0.7);
  margin: 0 0 36px;
}

.btn-register {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 140px;
  height: 44px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 600;
  color: #1E5EFF;
  background-color: #ffffff;
  border: 2px solid #ffffff;
  letter-spacing: 0.5px;
  transition: all 0.25s ease;

  &:hover {
    background-color: transparent;
    color: #ffffff;
    transform: translateY(-2px);
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
  }
}

// ========== 右侧表单面板 (40%) ==========
.form-panel {
  flex: 0 0 40%;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #ffffff;
  padding: 40px;
}

.form-inner {
  width: 100%;
  max-width: 360px;
}

.form-title {
  font-size: 28px;
  font-weight: 700;
  color: #1D2129;
  margin: 0 0 32px;
  letter-spacing: -0.3px;
}

.login-form {
  :deep(.el-form-item__label) {
    color: #4E5969;
    font-weight: 500;
    font-size: 13px;
    padding-bottom: 6px !important;
  }
}

// 输入框：浅灰底 + 前缀图标
.login-input {
  width: 100%;

  :deep(.el-input__wrapper) {
    background-color: #F2F3F5 !important;
    border: 1px solid transparent !important;
    border-radius: 8px !important;
    box-shadow: none !important;
    padding: 2px 12px !important;
    transition: border-color 0.2s ease, box-shadow 0.2s ease !important;

    &:hover {
      border-color: rgba(30, 94, 255, 0.3) !important;
    }

    &.is-focus {
      border-color: #1E5EFF !important;
      box-shadow: 0 0 0 3px rgba(30, 94, 255, 0.1) !important;
      background-color: #F2F3F5 !important;
    }
  }

  :deep(.el-input__inner) {
    color: #1D2129 !important;
    font-size: 14px;
    background-color: transparent !important;

    &::placeholder {
      color: #86909C !important;
    }
  }

  :deep(.el-input__prefix) {
    margin-right: 8px;
    color: #86909C !important;
  }
}

.input-icon {
  font-size: 16px;
  color: #86909C;
}

// 验证码输入
.captcha-input {
  :deep(.el-input-group__append) {
    background-color: transparent !important;
    border: none !important;
    border-radius: 0 8px 8px 0 !important;
    padding: 0 !important;
    overflow: hidden;
  }
}

.captcha {
  cursor: pointer;
  width: 120px;
  height: 34px;
  object-fit: cover;
  border-radius: 0 8px 8px 0;
}

// 登录按钮
.btn-login {
  width: 100%;
  height: 46px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 700;
  letter-spacing: 3px;
  background-color: #1E5EFF !important;
  border-color: #1E5EFF !important;
  box-shadow: 0 4px 14px rgba(30, 94, 255, 0.35) !important;
  margin-top: 8px;
  transition: all 0.25s ease !important;

  &:hover {
    background-color: #4070FF !important;
    border-color: #4070FF !important;
    transform: translateY(-1px);
    box-shadow: 0 6px 20px rgba(30, 94, 255, 0.5) !important;
  }
}

// 第三方登录
.social-login {
  margin-top: 24px;
  text-align: center;
}

.social-label {
  font-size: 12px;
  color: #86909C;
  margin: 0 0 14px;
}

.social-icons {
  display: flex;
  justify-content: center;
  gap: 12px;
}

.social-btn {
  width: 42px;
  height: 42px;
  border-radius: 50% !important;
  border: 1px solid #E4E7ED !important;
  background-color: #ffffff !important;
  display: inline-flex !important;
  align-items: center !important;
  justify-content: center !important;
  padding: 0 !important;
  box-shadow: none !important;
  transition: all 0.2s ease !important;

  &:hover {
    border-color: #1E5EFF !important;
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(30, 94, 255, 0.15) !important;
  }
}

// OIDC 快捷入口
.oidc-divider {
  margin-top: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.oidc-quick-btn {
  width: 100%;
  height: 40px;
  border-radius: 8px !important;
  border: 1px solid #E4E7ED !important;
  background-color: #F2F3F5 !important;
  color: #4E5969 !important;
  font-size: 13px;
  font-weight: 500;
  display: flex !important;
  align-items: center !important;
  justify-content: center;
  gap: 8px;
  box-shadow: none !important;
  transition: all 0.2s ease !important;

  &:hover {
    border-color: #1E5EFF !important;
    color: #1E5EFF !important;
    background-color: #EBF0FF !important;
  }
}

.oidc-quick-icon {
  width: 16px;
  height: 16px;
}
</style>
