<template>
  <view :class="cn('wd-divider flex relative items-center py-0 px-[15px] text-black/45 text-sm', customClass)"
    :style="customStyle">
    <view class="wd-divider__line block flex-1 h-px translate-y-1/2 bg-black/15"
      :style="color ? 'background: ' + color : ''">
    </view>
    <view class="wd-divider__content py-0 px-3" :style="color ? 'color: ' + color : ''">
      <slot></slot>
    </view>
    <view class="wd-divider__line block flex-1 h-px translate-y-1/2 bg-black/15"
      :style="color ? 'background: ' + color : ''"></view>
  </view>
</template>
<script lang="ts">
export default {
  name: 'wd-divider',
  options: {
    virtualHost: true,
    addGlobalClass: true,

  }
}
</script>

<script lang="ts" setup>
import { dividerProps } from './types'
import { cn } from '../common/cn'

defineProps(dividerProps)
</script>
