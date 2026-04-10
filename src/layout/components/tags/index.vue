<template>
  <el-tag v-for="(t, i) in tags"
          :key="t.name"
          class="tag"
          :closable="t.closeable"
          @close="close(t)"
          @click="toTag(t)"
          :type="t.active?'primary':'info'"
          effect="plain">
    {{ T(t.title) }}
  </el-tag>
</template>

<script>
  import { defineComponent, ref, onMounted, watch } from 'vue'
  import { useTagsStore } from '@/store/tags'
  import { useRoute, useRouter } from 'vue-router'
  import { T } from '@/utils/i18n'

  export default defineComponent({
    name: 'Index',
    setup () {
      const tags = ref([])
      const tagsStore = useTagsStore()
      const route = useRoute()
      const router = useRouter()
      tags.value = tagsStore.tags

      const addTag = (route) => {
        if (!route.meta?.hide && route.name) {
          tagsStore.addTag(route)
        }
      }
      const close = (tag) => {
        tagsStore.removeTag(tag)
        if (tag.active) {
          toLastTag()
        }
      }
      const toLastTag = () => {
        if (tags.value.length) {
          router.push({ name: tags.value[tags.value.length - 1].name })
        }
      }
      const init = () => {
        if (!tagsStore.tags.length) {
          tagsStore.initTags()
        }
        addTag(route)
      }

      const toTag = (tag) => {
        if (tag.name !== route.name) {
          router.push({ name: tag.name })
        }
      }

      onMounted(init)
      watch(route, (val) => {
        addTag(val)
      })
      return {
        tags,
        addTag,
        close,
        toLastTag,
        toTag,
        T,
      }
    },
  })
</script>

<style lang="scss" scoped>
// 容器使用 flex gap 控制间距，彻底替代 margin
.header-tags {
  display: flex !important;
  align-items: center !important;
  flex-wrap: wrap;
  gap: 4px !important;
  padding: 6px 12px !important;
}

.tag {
  border-radius: 4px;
  cursor: pointer;
  margin: 0 !important;
  border: 1px solid var(--gray-border);
  background-color: var(--gray-white);
  color: var(--gray-2);
  font-size: 14px;
  font-weight: 500;
  transition: all 0.2s cubic-bezier(0.25, 0.8, 0.25, 1);
  padding: 0 14px !important;
  height: 36px;
  line-height: 34px;

  &:hover {
    border-color: var(--primary);
    color: var(--primary);
    background-color: var(--primary-bg);
  }

  &.active {
    background-color: var(--primary-bg) !important;
    border-color: var(--primary) !important;
    color: var(--primary) !important;
    font-weight: 600;

    &:hover {
      background-color: var(--primary-bg);
      border-color: var(--primary);
      color: var(--primary);
    }
  }
}
</style>
