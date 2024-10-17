<template>
  <view :class="cn('wd-cell-group bg-white', border ? 'is-border' : '', customClass)" :style="customStyle">
    <view v-if="title || value || useSlot" :class='cn("wd-cell-group__title relative flex justify-between py-[13px] px-[15px] bg-white text-base text-black/85 font-medium leading-[1.43]",
      {
        "after:absolute after:left-0 after:bottom-0 after:w-full after:h-[1px] after:bg-[#e8e8e8] after:scale-y-1/2 after:block after:contents": border
      }
    )'>
      <!--左侧标题-->
      <view class="wd-cell-group__left">
        <text v-if="title">{{ title }}</text>
        <slot v-else name="title"></slot>
      </view>
      <!--右侧标题-->
      <view class="wd-cell-group__right text-[#262626] text-sm">
        <text v-if="value">{{ value }}</text>
        <slot v-else name="value"></slot>
      </view>
    </view>
    <view class="wd-cell-group__body bg-white">
      <slot></slot>
    </view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-cell-group',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import { useChildren } from '../composables/useChildren'
import { CELL_GROUP_KEY, cellGroupProps } from './types'
import { cn } from '../common/cn'
const props = defineProps(cellGroupProps)

const { linkChildren } = useChildren(CELL_GROUP_KEY)

linkChildren({ props })
</script>
