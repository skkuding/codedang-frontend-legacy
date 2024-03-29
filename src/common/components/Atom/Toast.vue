<script setup lang="ts">
import { onTrigger } from '@/common/composables/toast'
import type { ToastOption } from '@/common/composables/toast'
import { ref } from 'vue'
import IconCircleCheck from '~icons/fa6-solid/circle-check'
import IconCircleInfo from '~icons/fa6-solid/circle-info'
import IconCircleXmark from '~icons/fa6-solid/circle-xmark'
import IconTriangleExclamation from '~icons/fa6-solid/triangle-exclamation'

interface ToastItem extends ToastOption {
  id: symbol
}

const toastItems = ref<ToastItem[]>([])

onTrigger((option) => {
  const id = Symbol()
  toastItems.value.unshift({ id, ...option })
  setTimeout(() => {
    const index = toastItems.value.findIndex((value) => value.id === id)
    toastItems.value.splice(index, 1)
  }, option.duration || 3000)
})

const classMapper = {
  default: 'bg-white text-gray-dark',
  info: 'bg-[#6b7280] text-white',
  success: 'bg-green text-white',
  warn: 'bg-[#eab308] text-white',
  error: 'bg-[#ef4444] text-white'
}

const iconMapper = {
  info: IconCircleInfo,
  success: IconCircleCheck,
  warn: IconTriangleExclamation,
  error: IconCircleXmark
}
</script>

<template>
  <section
    class="pointer-events-none fixed inset-x-0 top-0 z-50 grid justify-items-center gap-2 pt-4"
  >
    <transition-group
      move-class="transition-all duration-300"
      enter-active-class="transition-all duration-300"
      leave-active-class="transition-all duration-300"
      enter-from-class="opacity-0 -translate-y-6"
      leave-to-class="opacity-0 -translate-y-6"
    >
      <output
        v-for="item in toastItems"
        :key="item.id"
        role="status"
        class="flex items-center gap-2 rounded px-4 py-1 shadow"
        :class="classMapper[item.type || 'default']"
      >
        <component :is="iconMapper[item.type]" v-if="item.type" />
        {{ item.message }}
      </output>
    </transition-group>
  </section>
</template>
