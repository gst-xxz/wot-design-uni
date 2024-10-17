<template>
  <view :class="cn('wd-steps text-[0] ', customClass, vertical ? 'is-vertical block' : '')">
    <slot />
  </view>
</template>
<script lang="ts">
export default {
  name: 'wd-steps',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>
<script lang="ts" setup>
import { useChildren } from '../composables/useChildren'
import { STEPS_KEY, stepsProps } from './types'
import { cn } from '../common/cn'

const props = defineProps(stepsProps)

const { linkChildren } = useChildren(STEPS_KEY)

linkChildren({ props })
</script>