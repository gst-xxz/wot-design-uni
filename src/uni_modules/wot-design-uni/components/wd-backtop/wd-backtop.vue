<template>
  <wd-transition :show="show" name="fade">
    <view :class="cn(
      `wd-backtop fixed bg-[#e1e1e1] w-10 h-10 flex justify-center items-center text-[#323233]`,
      customClass,
      { 'rounded-full': shape === 'circle', 'rounded': shape === 'square' }
    )" :style="`z-index: ${zIndex}; bottom: ${bottom}px; right: ${right}px; ${customStyle}`" @click="handleBacktop">
      <slot v-if="$slots.default"></slot>
      <wd-icon v-else custom-class="wd-backtop__backicon text-xl" name="backtop" :custom-style="iconStyle" />
    </view>
  </wd-transition>
</template>

<script lang="ts">
export default {
  name: 'wd-backtop',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import wdTransition from '../wd-transition/wd-transition.vue'
import wdIcon from '../wd-icon/wd-icon.vue'
import { computed } from 'vue'
import { backtopProps } from './types'
import { cn } from '../common/cn'

const props = defineProps(backtopProps)

const show = computed(() => props.scrollTop > props.top)

function handleBacktop() {
  uni.pageScrollTo({
    scrollTop: 0,
    duration: props.duration
  })
}
</script>
