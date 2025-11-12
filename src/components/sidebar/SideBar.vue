<template>
  <div
    class="sidebar bg-base-200 text-base-content scrollbar-hidden h-full overflow-x-hidden p-2 transition-all"
    :class="isSidebarCollapsed ? 'w-18 px-0' : 'w-64'"
  >
    <div :class="twMerge('flex h-full flex-col gap-2', isSidebarCollapsed ? 'w-18 px-0' : 'w-60')">
      <ul class="menu w-full flex-1">
        <li
          v-for="r in renderRoutes"
          :key="r"
          @mouseenter="(e) => mouseenterHandler(e, r)"
        >
          <a
            :class="[
              r === route.name ? 'menu-active' : '',
              isSidebarCollapsed && 'justify-center',
              'py-2',
            ]"
            @click.passive="() => router.push({ name: r })"
          >
            <component
              :is="ROUTE_ICON_MAP[r]"
              class="h-5 w-5"
            />
            <template v-if="!isSidebarCollapsed">
              {{ $t(r) }}
            </template>
          </a>
        </li>
      </ul>
      <template v-if="isSidebarCollapsed">
        <VerticalInfos v-if="showStatisticsWhenSidebarCollapsed">
          <div class="flex w-full flex-col items-center justify-center gap-2">
            <button
              class="btn btn-circle btn-sm bg-base-300"
              @click="showBackendSelectorDialog = true"
              @mouseenter="(e) => showTip(e, t('selectBackend'), { placement: 'right' })"
            >
              <ServerIcon class="h-5 w-5" />
            </button>
            <button
              class="btn btn-circle btn-sm bg-base-300"
              @click="isSidebarCollapsed = false"
            >
              <ArrowRightCircleIcon class="h-5 w-5" />
            </button>
          </div>
        </VerticalInfos>
        <div
          class="flex w-full flex-col items-center justify-center gap-2"
          v-else
        >
          <button
            class="btn btn-circle btn-sm bg-base-300"
            @click="showBackendSelectorDialog = true"
            @mouseenter="(e) => showTip(e, t('selectBackend'), { placement: 'right' })"
          >
            <ServerIcon class="h-5 w-5" />
          </button>
          <button
            class="btn btn-circle btn-sm bg-base-300"
            @click="isSidebarCollapsed = false"
          >
            <ArrowRightCircleIcon class="h-5 w-5" />
          </button>
        </div>
      </template>
      <template v-else>
        <OverviewCarousel v-if="route.name !== ROUTE_NAME.overview" />
        <div class="card">
          <CommonSidebar />
        </div>
      </template>
    </div>

    <DialogWrapper
      v-model="showBackendSelectorDialog"
      :title="$t('selectBackend')"
    >
      <BackendSwitch :disable-edit-backend="true" />
    </DialogWrapper>
  </div>
</template>

<script setup lang="ts">
import DialogWrapper from '@/components/common/DialogWrapper.vue'
import BackendSwitch from '@/components/settings/BackendSwitch.vue'
import CommonSidebar from '@/components/sidebar/CommonCtrl.vue'
import { ROUTE_ICON_MAP, ROUTE_NAME } from '@/constant'
import { renderRoutes } from '@/helper'
import { useTooltip } from '@/helper/tooltip'
import router from '@/router'
import { isSidebarCollapsed, showStatisticsWhenSidebarCollapsed } from '@/store/settings'
import { ArrowRightCircleIcon, ServerIcon } from '@heroicons/vue/24/outline'
import { twMerge } from 'tailwind-merge'
import { ref } from 'vue'
import { useI18n } from 'vue-i18n'
import { useRoute } from 'vue-router'
import OverviewCarousel from './OverviewCarousel.vue'
import VerticalInfos from './VerticalInfos.vue'

const { showTip } = useTooltip()
const { t } = useI18n()

const showBackendSelectorDialog = ref(false)

const mouseenterHandler = (e: MouseEvent, r: string) => {
  if (!isSidebarCollapsed.value) return
  showTip(e, t(r), {
    placement: 'right',
  })
}

const route = useRoute()
</script>
