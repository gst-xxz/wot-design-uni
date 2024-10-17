<template>
  <view
    :class="cn('wd-radio-group', 'bg-white text-[0]', customClass, cell && shape === 'button' ? 'is-button w-full pt-2 pr-[3px] pb-5 pl-[15px] box-border overflow-hidden h-auto' : '')"
    :style="customStyle">
    <slot />
  </view>
</template>
<script lang="ts">
export default {
  name: 'wd-radio-group',
  options: {
    virtualHost: true,
  }
}
</script>

<script lang="ts" setup>
import { watch } from 'vue'
import { useChildren } from '../composables/useChildren'
import { RADIO_GROUP_KEY, radioGroupProps } from './types'
import { cn } from '../common/cn'

const props = defineProps(radioGroupProps)
const emit = defineEmits(['change', 'update:modelValue'])

const { linkChildren, children } = useChildren(RADIO_GROUP_KEY)

linkChildren({ props, updateValue })

watch(
  () => props.shape,
  (newValue) => {
    // type: 'dot', 'button', 'check'
    const type = ['check', 'dot', 'button']
    if (type.indexOf(newValue) === -1) console.error(`shape must be one of ${type.toString()}`)
  },
  { deep: true, immediate: true }
)

/**
 * @description 处理radio子节点通知
 */
function updateValue(value: string | number | boolean) {
  emit('update:modelValue', value)
  emit('change', {
    value
  })
}
</script>