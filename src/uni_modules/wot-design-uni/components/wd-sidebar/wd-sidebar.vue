<template>
  <view :class="cn('wd-sidebar flex flex-col overflow-y-auto w-[104px] h-full bg-white ', customClass)"
    :style="customStyle">
    <slot></slot>
    <view class="wd-sidebar__padding flex-auto bg-gray-1"></view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-sidebar',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import { useChildren } from '../composables/useChildren'
import { SIDEBAR_KEY, sidebarProps } from './types'
import { cn } from '../common/cn'
const props = defineProps(sidebarProps)
const emit = defineEmits(['change', 'update:modelValue'])

const { linkChildren } = useChildren(SIDEBAR_KEY)
linkChildren({ props, setChange })

/**
 * 子项状态变更
 * @param child 子项
 */
function setChange(value: number | string, label: string) {
  emit('update:modelValue', value)
  emit('change', { value, label })
}
</script>
