<template>
  <div class="login-page">
    <!-- ========== 左侧品牌面板 ========== -->
    <div class="brand-panel">
      <div class="brand-inner">
        <img src="@/assets/logo.png" alt="logo" class="brand-logo"/>
        <h1 class="brand-title">New here?</h1>
        <p class="brand-subtitle">
          Lorem ipsum dolor sit amet, consectetur adipiscing elit.<br/>
          Join our platform to manage your remote connections seamlessly.
        </p>
        <el-button class="btn-register" @click="toLogin">
          登录
        </el-button>
      </div>
    </div>

    <!-- ========== 右侧注册表单面板 ========== -->
    <div class="form-panel">
      <div class="form-inner">
        <h2 class="form-title">注册</h2>

        <el-form ref="f" :model="form" label-position="top" class="login-form" :rules="rules">
          <el-form-item :label="T('Username')" prop="username">
            <el-input v-model="form.username" class="login-input" placeholder="用户名">
              <template #prefix><el-icon class="input-icon"><User /></el-icon></template>
            </el-input>
          </el-form-item>

          <el-form-item :label="T('Email')" prop="email">
            <el-input v-model="form.email" class="login-input" placeholder="邮箱">
              <template #prefix><el-icon class="input-icon"><Message /></el-icon></template>
            </el-input>
          </el-form-item>

          <el-form-item :label="T('Password')" prop="password">
            <el-input v-model="form.password" type="password" show-password class="login-input" placeholder="密码">
              <template #prefix><el-icon class="input-icon"><Lock /></el-icon></template>
            </el-input>
          </el-form-item>

          <el-form-item :label="T('ConfirmPassword')" prop="confirm_password">
            <el-input v-model="form.confirm_password" type="password" @keyup.enter.native="submit" show-password class="login-input" placeholder="确认密码">
              <template #prefix><el-icon class="input-icon"><Lock /></el-icon></template>
            </el-input>
          </el-form-item>

          <el-button @click="submit" type="primary" class="btn-submit">
            注册
          </el-button>
        </el-form>
      </div>
    </div>
  </div>
</template>

<script setup>
  import { reactive, ref } from 'vue'
  import { ElMessage } from 'element-plus'
  import { T } from '@/utils/i18n'
  import { useRouter } from 'vue-router'
  import { register } from '@/api/user'
  import { useUserStore } from '@/store/user'
  import { useAppStore } from '@/store/app'
  import { User, Lock, Message } from '@element-plus/icons-vue'

  const router = useRouter()
  const userStore = useUserStore()

  const form = reactive({
    username: '',
    email: '',
    password: '',
    confirm_password: '',
  })

  const rules = {
    username: [
      { required: true, message: T('ParamRequired', { param: T('Username') }), trigger: 'blur' },
    ],
    password: [
      { required: true, message: T('ParamRequired', { param: T('Password') }), trigger: 'blur' },
    ],
    confirm_password: [
      { required: true, message: T('ParamRequired', { param: T('ConfirmPassword') }), trigger: 'blur' },
      {
        validator: (rule, value, callback) => {
          if (value !== form.password) {
            callback(new Error(T('PasswordNotMatchConfirmPassword')))
          } else {
            callback()
          }
        }, trigger: 'blur',
      },
    ],
  }

  const f = ref(null)
  const submit = async () => {
    const v = await f.value.validate().catch(_ => false)
    if (!v) return
    const res = await register(form).catch(_ => false)
    if (!res) return
    userStore.saveUserData(res.data)
    useAppStore().loadConfig()
    ElMessage.success('注册成功')
    router.push('/')
  }

  const toLogin = () => router.push('/login')
</script>

<style scoped lang="scss">
.login-page {
  display: flex;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}

.brand-panel {
  flex: 0 0 60%;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #4F8EFF 0%, #1E5EFF 40%, #0A2E8C 100%);
  overflow: hidden;

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

.btn-submit {
  width: 100%;
  height: 46px;
  border-radius: 8px;
  font-size: 15px;
  font-weight: 700;
  letter-spacing: 2px;
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
</style>
