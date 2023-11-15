<script setup lang='ts'>
import type { CSSProperties } from 'vue'
import { computed, ref, watch } from 'vue'
import { NButton, NLayoutSider, NPopover, NGi, NGrid } from 'naive-ui'
import List from './List.vue'
import Footer from './Footer.vue'
import { useAppStore, useChatStore } from '@/store'
import { useBasicLayout } from '@/hooks/useBasicLayout'
import { PromptStore } from '@/components/common'

const appStore = useAppStore()
const chatStore = useChatStore()

const { isMobile } = useBasicLayout()
const show = ref(false)

const collapsed = computed(() => appStore.siderCollapsed)

function handleAdd() {
  chatStore.addHistory({ title: 'New Chat', uuid: Date.now(), isEdit: false })
  if (isMobile.value)
    appStore.setSiderCollapsed(true)
}

function handleUpdateCollapsed() {
  appStore.setSiderCollapsed(!collapsed.value)
}

const getMobileClass = computed<CSSProperties>(() => {
  if (isMobile.value) {
    return {
      position: 'fixed',
      zIndex: 50,
    }
  }
  return {}
})

const mobileSafeArea = computed(() => {
  if (isMobile.value) {
    return {
      paddingBottom: 'env(safe-area-inset-bottom)',
    }
  }
  return {}
})

watch(
  isMobile,
  (val) => {
    appStore.setSiderCollapsed(val)
  },
  {
    immediate: true,
    flush: 'post',
  },
)
</script>

<template>
  <NLayoutSider
    :collapsed="collapsed"
    :collapsed-width="0"
    :width="260"
    :show-trigger="isMobile ? false : 'arrow-circle'"
    collapse-mode="transform"
    position="absolute"
    bordered
    :style="getMobileClass"
    @update-collapsed="handleUpdateCollapsed"
  >
    <div class="flex flex-col h-full" :style="mobileSafeArea">
      <main class="flex flex-col flex-1 min-h-0">
        <div class="p-4">
          <NButton dashed block @click="handleAdd">{{ $t('chat.newChatButton') }}</NButton>
        </div>
        <div class="flex-1 min-h-0 pb-4 overflow-hidden">
          <List />
        </div>
        <div class="p-4">
          <NButton block @click="show = true">{{ $t('store.siderButton') }}</NButton>
        </div>
        <div class="flex p-4 pt-0 justify-center flex-wrap">
          <div class="p-4 pt-0">
            <NPopover trigger="hover">
              <template #trigger>
                <NButton block class="mb-4 text-blue-500">关注站长公众号，获取最新动态</NButton>
              </template>
              <img class="code_img" src="@assets/image/qrcode_for_gh_22cba7f8028a_258.jpg" alt />
            </NPopover>
          </div>
          <div>
            <NPopover trigger="hover">
              <template #trigger>
                <NButton class="flex-1 m-l-0 text-blue-500">赞助</NButton>
              </template>
              <NGrid x-gap="12" :cols="2" >
                <NGi>
                  <div class="flex-1">
                    <img class="code_img" src="@assets/image/wx.jpg" alt />
                    <p class="text-center">微信</p>
                  </div>
                </NGi>
                <NGi>
                  <div class="flex-1">
                    <img class="code_img" src="@assets/image/zfb.jpg" alt />
                    <p class="text-center">支付宝</p>
                  </div>
                </NGi>
              </NGrid>
            </NPopover>
          </div>
        </div>
      </main>
      <Footer />
    </div>
  </NLayoutSider>
  <template v-if="isMobile">
    <div v-show="!collapsed" class="fixed inset-0 z-40 bg-black/40" @click="handleUpdateCollapsed" />
  </template>
  <PromptStore v-model:visible="show" />
</template>
<style scoped>
.code_img {
  width: 260px;
  display: block;
  margin: 0 auto;
}
</style>