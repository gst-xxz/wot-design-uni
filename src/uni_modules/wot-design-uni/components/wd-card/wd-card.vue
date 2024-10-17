<template>
  <view :class="cn(
    'wd-card py-0 px-[15px] bg-white leading-[1.1] my-0 mx-[15px] rounded-lg shadow-[0px_4px_8px_0px_rgba(0,0,0,.02)] text-sm mb-3',
    type == 'rectangle' ? 'is-rectangle mx-0 rounded-none shadow-none' : '',
    customClass
  )" :style="customStyle">
    <view :class="cn(
      'wd-card__title-content py-4 px-0 text-black/85 text-base',
      'after:absolute after:left-0 after:top-0 after:w-full after:h-[1px] after:bg-black/10 after:scale-y-[0.5] after:block',
      customTitleClass,
      { 'text-sm': type == 'rectangle' }
    )">
      <view class="wd-card__title">
        <text v-if="title">{{ title }}</text>
        <slot v-else name="title"></slot>
      </view>
    </view>
    <view
      :class="cn('wd-card__content text-black/45 leading-[1.428]', customContentClass, { 'relative py-3 px-0': type == 'rectangle' })">
      <slot></slot>
    </view>
    <view :class="cn(
      'wd-card__footer pt-3 pb-4 px-0 text-right',
      customFooterClass,
      {
        'relative py-3 px-0': type == 'rectangle',
        'after:absolute after:left-0 after:top-0 after:w-full after:h-[1px] after:bg-black/10 after:scale-y-[0.5] after:block': type == 'rectangle'
      }
    )">
      <slot name="footer"></slot>
    </view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-card',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import { cardProps } from './types'
import { cn } from '../common/cn'

defineProps(cardProps)
</script>
