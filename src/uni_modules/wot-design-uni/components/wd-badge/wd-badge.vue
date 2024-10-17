<template>
  <view :class="cn('wd-badge relative align-middle inline-block', customClass)" :style="customStyle">
    <slot></slot>
    <view v-if="isBadgeShow" :class="cn(
      'wd-badge__content inline-block h-4 leading-4 py-0 px-[5px] text-center text-[#fff] bg-danger rounded-[10px] text-xs whitespace-nowrap border-[2px] border-solid border-white font-medium',
      'is-fixed absolute translate-x-1/2 -translate-y-1/2',
      {
        'bg-primary': type === 'primary',
        'bg-success': type === 'success',
        'bg-warning': type === 'warning',
        'bg-info': type === 'info',
        'bg-danger': type === 'danger',
      },
      isDot ? 'is-dot w-1.5 h-1.5 p-0 rounded-full' : ''
    )" :style="contentStyle">
      {{ content }}
    </view>
  </view>
</template>
<script lang="ts">
export default {
  name: 'wd-badge',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>
<script lang="ts" setup>
import { computed, ref, watch } from 'vue'
import { badgeProps } from './types'
import { cn } from '../common/cn'

const props = defineProps(badgeProps)
const content = ref<number | string | null>(null)

watch(
  [() => props.modelValue, () => props.max, () => props.isDot],
  () => {
    notice()
  },
  { immediate: true, deep: true }
)

const contentStyle = computed(() => {
  return `background-color: ${props.bgColor};top:${props.top || 0}px;right:${props.right || 0}px`
})

// 是否展示徽标数字
const isBadgeShow = computed(() => {
  let isBadgeShow: boolean = false
  if (!props.hidden && (content.value || (content.value === 0 && props.showZero) || props.isDot)) {
    isBadgeShow = true
  }
  return isBadgeShow
})

function notice() {
  if (props.isDot) return
  let value = props.modelValue
  const max = props.max
  if (value && max && typeof value === 'number' && !Number.isNaN(value) && !Number.isNaN(max)) {
    value = max < value ? `${max}+` : value
  }
  content.value = value
}
</script>