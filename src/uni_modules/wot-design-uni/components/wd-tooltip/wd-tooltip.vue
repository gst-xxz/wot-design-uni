<template>
  <view :class="cn(`wd-tooltip relative inline-block`, customClass)" :style="customStyle" id="tooltip"
    @click.stop="popover.noop">
    <!-- 用于为渲染获取宽高的元素 -->
    <view
      class="wd-tooltip__pos absolute min-w-[138px] min-h-9 text-sm blur-[10px] bg-clip-padding rounded-lg bg-[rgba(38,39,40,.8)] text-white text-center box-border z-[500] wd-tooltip__hidden -left-[100vw] -bottom-[100vh] invisible"
      id="pos">
      <view class="wd-tooltip__container text-sm leading-[18px]">
        <view v-if="!useContentSlot" class="wd-tooltip__inner pt-[9px] px-5 whitespace-nowrap leading-[18px]">{{ content
          }}</view>
      </view>
    </view>
    <wd-transition
      custom-class="wd-tooltip__pos absolute min-w-[138px] min-h-9 text-sm blur-[10px] bg-clip-padding rounded-lg bg-[rgba(38,39,40,.8)] text-white text-center box-border z-[500]"
      :custom-style="popover.popStyle.value" :show="showTooltip" name="fade" :duration="200">
      <view class="wd-tooltip__container text-sm leading-[18px]">
        <view v-if="visibleArrow"
          :class="cn(`wd-tooltip__arrow absolute w-0 h-0 ${popover.arrowClass.value} ${customArrow}`)"
          :style="popover.arrowStyle.value"></view>
        <!-- 普通模式 -->
        <view v-if="!useContentSlot" class="wd-tooltip__inner pt-[9px] px-5 whitespace-nowrap leading-[18px]">{{ content
          }}</view>
        <!-- 用户自定义样式 -->
        <slot name="content" v-else />
      </view>
      <wd-icon v-if="showClose" name="close"
        custom-class="wd-tooltip__close-icon text-xs absolute -right-2 -top-2.5 scale-50 p-2.5"
        @click="toggle"></wd-icon>
    </wd-transition>
    <view @click="toggle" class="wd-tooltip__target" id="target">
      <slot />
    </view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-tooltip',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import wdTransition from '../wd-transition/wd-transition.vue'
import { getCurrentInstance, inject, onBeforeMount, onBeforeUnmount, onMounted, ref, watch } from 'vue'
import { usePopover } from '../composables/usePopover'
import { closeOther, pushToQueue, removeFromQueue } from '../common/clickoutside'
import { type Queue, queueKey } from '../composables/useQueue'
import { tooltipProps, type TooltipExpose } from './types'
import { cn } from '../common/cn'

const props = defineProps(tooltipProps)
const emit = defineEmits(['update:modelValue', 'menuclick', 'change', 'open', 'close'])

const popover = usePopover()
const queue = inject<Queue | null>(queueKey, null)
const selector: string = 'tooltip'
const { proxy } = getCurrentInstance() as any
const showTooltip = ref<boolean>(false) // 控制tooltip显隐

watch(
  () => props.content,
  (newVal) => {
    if (newVal === null || newVal === undefined) {
      // eslint-disable-next-line prettier/prettier
      console.error('[wot-design] warning(wd-tooltip): content can\'t be null or undefined')
    }
  }
)

watch(
  () => props.placement,
  () => {
    popover.init(props.placement, props.visibleArrow, selector)
  }
)

watch(
  () => props.modelValue,
  (newValue) => {
    showTooltip.value = newValue
  }
)

watch(
  () => showTooltip.value,
  (newValue) => {
    if (newValue) {
      popover.control(props.placement, props.offset)
      if (queue && queue.closeOther) {
        queue.closeOther(proxy)
      } else {
        closeOther(proxy)
      }
    }
    popover.showStyle.value = newValue ? 'display: inline-block;' : 'display: none;'
    emit('change', { show: newValue })
    emit(`${newValue ? 'open' : 'close'}`)
  }
)

onMounted(() => {
  popover.init(props.placement, props.visibleArrow, selector)
})

onBeforeMount(() => {
  if (queue && queue.pushToQueue) {
    queue.pushToQueue(proxy)
  } else {
    pushToQueue(proxy)
  }
  popover.showStyle.value = props.modelValue ? 'opacity: 1;' : 'opacity: 0;'
})

onBeforeUnmount(() => {
  if (queue && queue.removeFromQueue) {
    queue.removeFromQueue(proxy)
  } else {
    removeFromQueue(proxy)
  }
})

function toggle() {
  if (props.disabled) return
  updateModelValue(!showTooltip.value)
}

function open() {
  updateModelValue(true)
}

function close() {
  updateModelValue(false)
}

function updateModelValue(value: boolean) {
  showTooltip.value = value
  emit('update:modelValue', value)
}

defineExpose<TooltipExpose>({
  open,
  close
})
</script>
<style>
.wd-tooltip__arrow-down {
  border-left: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-right: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-top: var(--wot-tooltip-arrow-size, 5px) solid var(--wot-tooltip-bg, rgba(38, 39, 40, .8));
  -webkit-transform: translateX(-50%);
  transform: translate(-50%);
  bottom: calc(-1 * var(--wot-tooltip-arrow-size, 5px))
}

.wd-tooltip__arrow-up {
  border-left: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-right: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-bottom: var(--wot-tooltip-arrow-size, 5px) solid var(--wot-tooltip-bg, rgba(38, 39, 40, .8));
  -webkit-transform: translateX(-50%);
  transform: translate(-50%);
  top: calc(-1 * var(--wot-tooltip-arrow-size, 5px))
}

.wd-tooltip__arrow-left {
  border-top: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-bottom: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-right: var(--wot-tooltip-arrow-size, 5px) solid var(--wot-tooltip-bg, rgba(38, 39, 40, .8));
  -webkit-transform: translateY(-50%);
  transform: translateY(-50%);
  left: calc(-1 * var(--wot-tooltip-arrow-size, 5px))
}

.wd-tooltip__arrow-right {
  border-top: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-bottom: var(--wot-tooltip-arrow-size, 5px) solid transparent;
  border-left: var(--wot-tooltip-arrow-size, 5px) solid var(--wot-tooltip-bg, rgba(38, 39, 40, .8));
  -webkit-transform: translateY(-50%);
  transform: translateY(-50%);
  right: calc(-1 * var(--wot-tooltip-arrow-size, 5px))
}
</style>
