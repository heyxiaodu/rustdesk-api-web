<template>
  <el-menu
          class="menus"
          :collapse="isCollapse"
          :default-active="activeIndex"
          background-color="transparent"
          text-color="rgba(255, 255, 255, 0.85)"
          active-text-color="#ffffff"
          router
  >
    <menu-item v-for="(route,index) in routes" :key="route.name" :route="route"></menu-item>
  </el-menu>
</template>

<script>
  import { defineComponent, ref, onMounted, watch, computed } from 'vue'
  import { useRouteStore } from '@/store/router'
  import MenuItem from '@/layout/components/menu/item.vue'
  import { useRoute } from 'vue-router'
  import { useAppStore } from '@/store/app'

  export default defineComponent({
    name: 'Menu',
    created () {
    },
    components: { MenuItem },
    setup () {
      const routes = ref([])
      const route = useRoute()
      const app = useAppStore()
      const isCollapse = computed(() => app.setting.sideIsCollapse)
      const activeIndex = computed(() => route.name)

      routes.value = useRouteStore().routes
      return {
        routes,
        activeIndex,
        isCollapse,
      }
    },

  })
</script>

<style lang="scss" scoped>
  .menus {
    min-height: 100vh;
    border-right: none;
    background: var(--sidebar-bg);
    &:not(.el-menu--collapse) {
      width: var(--sideBarWidth);
    }
  }
</style>
<style>
  // 仅限侧边栏 .menus 容器内的样式，不会污染 Header
  .menus {
    background: var(--sidebar-bg) !important;
    border-right: none !important;

    .el-sub-menu__title,
    .el-menu-item {
      border-radius: 4px;
      margin: 2px 8px;
      padding-left: 16px !important;
      height: 38px;
      line-height: 38px;
      color: rgba(255, 255, 255, 0.85);
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
      position: relative;

      &:hover {
        background-color: var(--sidebar-hover) !important;
        color: #ffffff;
      }

      &.is-active {
        background-color: var(--sidebar-active) !important;
        color: #ffffff;
        font-weight: 600;
      }
    }

    .el-menu-item-group__title {
      color: rgba(255, 255, 255, 0.4);
      padding: 8px 16px;
      font-size: 11px;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
  }

  // 弹出子菜单（折叠时）
  .el-menu--popup {
    background: var(--sidebar-bg) !important;
    border-radius: 6px !important;
    border: 1px solid rgba(255, 255, 255, 0.1) !important;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.4) !important;
    padding: 4px 0 !important;
    min-width: 180px !important;

    .popper__arrow,
    .el-popper__arrow {
      display: none !important;
    }

    .el-menu-item,
    .el-sub-menu__title {
      color: rgba(255, 255, 255, 0.85) !important;
      background-color: transparent !important;
      border-radius: 4px;
      margin: 2px 8px;
      padding-left: 16px !important;
      height: 36px;
      line-height: 36px;
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);

      i, .el-icon {
        color: rgba(255, 255, 255, 0.6) !important;
      }

      &:hover {
        background-color: var(--sidebar-hover) !important;
        color: #ffffff !important;

        i, .el-icon {
          color: #ffffff !important;
        }
      }

      &.is-active {
        background-color: var(--sidebar-active) !important;
        color: #ffffff !important;
        font-weight: 600;

        i, .el-icon {
          color: #ffffff !important;
        }
      }
    }

    .el-menu-item-group__title {
      color: rgba(255, 255, 255, 0.4);
      padding: 4px 16px;
      font-size: 11px;
    }
  }
</style>
