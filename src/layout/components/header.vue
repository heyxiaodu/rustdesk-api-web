<template>
  <el-icon class="ex-icon" @click="expandOrFoldSlider">
    <el-icon-expand v-if="setting.sideIsCollapse"></el-icon-expand>
    <el-icon-fold v-else></el-icon-fold>
  </el-icon>
  <div class="header-logo">
    <img :src="setting.logo" alt="" class="logo">
    <div class="title">{{setting.title}}</div>
  </div>
  <Setting></Setting>
</template>

<script>
  import { defineComponent, computed } from 'vue'
  import HeaderMenu from '@/layout/components/menu/index.vue'
  import Setting from '@/layout/components/setting/index.vue'
  import { useAppStore } from '@/store/app'
  import GTags from '@/layout/components/tags/index.vue'

  export default defineComponent({
    name: 'LayerHeader',
    created () {
    },
    components: { HeaderMenu, Setting, GTags },
    watch: {},
    setup (props) {
      const appStore = useAppStore()
      const setting = computed(() => appStore.setting)
      const expandOrFoldSlider = () => {
        appStore.sideCollapse()
      }
      return {
        setting,
        expandOrFoldSlider,
      }
    },

  })
</script>

<style scoped lang="scss">
  .ex-icon {
    height: 100%;
    display: flex;
    align-items: center;
    margin-right: 12px;
    font-size: 18px;
    cursor: pointer;
    color: var(--gray-2);
    padding: 0 8px;
    border-radius: 4px;
    transition: all 0.2s ease;

    &:hover {
      background-color: var(--gray-bg);
      color: var(--primary);
    }
  }

  .header-logo {
    display: flex;
    height: 100%;
    align-items: center;

    .title {
      display: block;
      margin-left: 10px;
      color: var(--gray-1);
      font-weight: 600;
      font-size: 15px;
    }

    .logo {
      display: block;
      width: 28px;
      height: 28px;
    }
  }


</style>
<style lang="scss">

</style>
