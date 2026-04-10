<template>
  <div class="profile-page">
    <el-card class="profile-card" shadow="hover">
      <!-- 头像 + 用户名区 -->
      <div class="profile-header">
        <el-avatar :size="64" class="user-avatar">
          {{ userStore.username?.charAt(0)?.toUpperCase() || 'A' }}
        </el-avatar>
        <div class="profile-title">
          <div class="username">{{ userStore.username }}</div>
          <div class="sub-title">{{ T('Userinfo') }}</div>
        </div>
      </div>

      <!-- 用户信息列表 -->
      <el-descriptions :column="1" :label-align="'left'" class="profile-descriptions">
        <el-descriptions-item :label="T('Username')">
          <span class="value-primary">{{ userStore.username }}</span>
        </el-descriptions-item>
        <el-descriptions-item :label="T('Email')">
          <span :class="userStore.email ? 'value-primary' : 'value-placeholder'">
            {{ userStore.email || '未设置' }}
          </span>
        </el-descriptions-item>
        <el-descriptions-item :label="T('Password')">
          <el-button type="primary" plain @click="showChangePwd">
            {{ T('ChangePassword') }}
          </el-button>
        </el-descriptions-item>
      </el-descriptions>
    </el-card>

    <!-- OIDC 绑定区域 -->
    <el-card class="oidc-card" shadow="hover">
      <template #header>
        <div class="card-header-title">OIDC {{ T('Bind') }}</div>
      </template>
      <el-table :data="oidcData" border fit stripe>
        <el-table-column :label="T('IdP')" prop="op" align="center"></el-table-column>
        <el-table-column :label="T('Status')" prop="status" align="center">
          <template #default="{ row }">
            <el-tag v-if="row.status === 1" type="success">{{ T('HasBind') }}</el-tag>
            <el-tag v-else type="danger">{{ T('NoBind') }}</el-tag>
          </template>
        </el-table-column>
        <el-table-column :label="T('Actions')" align="center" width="200">
          <template #default="{ row }">
            <el-button v-if="row.status === 1" type="danger" size="small" @click="toUnBind(row)">{{ T('UnBind') }}</el-button>
            <el-button v-else type="success" size="small" @click="toBind(row)">{{ T('ToBind') }}</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-card>

    <changePwdDialog v-model:visible="changePwdVisible"></changePwdDialog>
  </div>
</template>

<script setup>
  import changePwdDialog from '@/components/changePwdDialog.vue'
  import { ref } from 'vue'
  import { useUserStore } from '@/store/user'
  import { bind, unbind } from '@/api/oauth'
  import { myOauth } from '@/api/user'
  import { ElMessageBox } from 'element-plus'
  import { T } from '@/utils/i18n'

  const userStore = useUserStore()
  const changePwdVisible = ref(false)
  const showChangePwd = () => {
    changePwdVisible.value = true
  }
  const oidcData = ref([])
  const getMyOauth = async () => {
    const res = await myOauth().catch(_ => false)
    if (res) {
      oidcData.value = res.data
    }
  }
  getMyOauth()
  const toBind = async (row) => {
    const res = await bind({ op: row.op }).catch(_ => false)
    if (res) {
      const { url } = res.data
      window.open(url)
    }
  }
  const toUnBind = async (row) => {
    const cf = await ElMessageBox.confirm(T('Confirm?', { param: T('UnBind') }), {
      confirmButtonText: T('Confirm'),
      cancelButtonText: T('Cancel'),
      type: 'warning',
    }).catch(_ => false)
    if (!cf) {
      return false
    }
    const res = await unbind({ op: row.op }).catch(_ => false)
    if (res) {
      getMyOauth()
    }
  }
</script>

<style scoped lang="scss">
.profile-page {
  max-width: 680px;
  margin: 0 auto;
  padding: 20px 16px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

// 头像区域
.profile-card {
  border-radius: 8px;

  :deep(.el-card__header) {
    display: none;
  }
}

.profile-header {
  display: flex;
  align-items: center;
  gap: 16px;
  padding-bottom: 20px;
  border-bottom: 1px solid var(--gray-border);
  margin-bottom: 8px;
}

.user-avatar {
  background-color: var(--primary-bg);
  color: var(--primary);
  font-size: 24px;
  font-weight: 700;
  flex-shrink: 0;
  border: 2px solid var(--primary-bg);
}

.profile-title {
  .username {
    font-size: 20px;
    font-weight: 700;
    color: var(--gray-1);
    line-height: 1.4;
  }
  .sub-title {
    font-size: 13px;
    color: var(--gray-3);
    margin-top: 2px;
  }
}

// 信息描述列表
.profile-descriptions {
  margin-top: 4px;

  :deep(.el-descriptions__label) {
    color: var(--gray-2);
    font-weight: 500;
    min-width: 80px;
    padding-right: 12px;
  }

  :deep(.el-descriptions__content) {
    color: var(--gray-1);
  }
}

.value-primary {
  color: var(--gray-1);
  font-weight: 500;
}

.value-placeholder {
  color: var(--gray-3);
  font-style: italic;
}

// OIDC 卡片
.oidc-card {
  border-radius: 8px;

  .card-header-title {
    font-size: 15px;
    font-weight: 600;
    color: var(--gray-1);
  }

  :deep(.el-table) {
    border-radius: 4px;
    overflow: hidden;
  }
}
</style>
