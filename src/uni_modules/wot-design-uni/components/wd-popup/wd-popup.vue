<template>
  <wd-overlay v-if="modal" :show="modelValue" :z-index="zIndex" :lock-scroll="lockScroll" :duration="duration"
    :custom-style="modalStyle" @click="handleClickModal" @touchmove="noop" />
  <view v-if="!lazyRender || inited" :class="rootClass" :style="style" @transitionend="onTransitionEnd">
    <slot />
    <wd-icon v-if="closable" custom-class="wd-popup__close absolute top-2.5 right-2.5 text-[#666] text-2xl -rotate-45"
      name="add" @click="close" />
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-popup',
  options: {
    virtualHost: true,
    addGlobalClass: true,

  }
}
</script>

<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import wdOverlay from '../wd-overlay/wd-overlay.vue'
import { computed, onBeforeMount, ref, watch } from 'vue'
import { isObj, requestAnimationFrame } from '../common/util'
import { popupProps } from './types'
import { cn } from '../common/cn'

const props = defineProps(popupProps)
const emit = defineEmits([
  'update:modelValue',
  'before-enter',
  'enter',
  'before-leave',
  'leave',
  'after-leave',
  'after-enter',
  'click-modal',
  'close'
])

const getClassNames = (name?: string) => {
  if (!name) {
    return {
      enter: 'enter-class enter-active-class',
      'enter-to': 'enter-to-class enter-active-class',
      leave: 'leave-class leave-active-class',
      'leave-to': 'leave-to-class leave-active-class'
    }
  }

  return {
    enter: `wd-${name}-enter wd-${name}-enter-active`,
    'enter-to': `wd-${name}-enter-to wd-${name}-enter-active`,
    leave: `wd-${name}-leave wd-${name}-leave-active`,
    'leave-to': `wd-${name}-leave-to wd-${name}-leave-active`
  }
}

// 初始化是否完成
const inited = ref<boolean>(false)
// 是否显示
const display = ref<boolean>(false)
// 当前动画状态
const status = ref<string>('')
// 动画是否结束
const transitionEnded = ref<boolean>(false)
// 动画持续时间
const currentDuration = ref<number>(300)
// 类名
const classes = ref<string>('')

const safeBottom = ref<number>(0)

const name = ref<string>('') // 动画名

const style = computed(() => {
  return `z-index: ${props.zIndex}; padding-bottom: ${safeBottom.value}px; -webkit-transition-duration: ${currentDuration.value
    }ms; transition-duration: ${currentDuration.value}ms; ${display.value || !props.hideWhenClose ? '' : 'display: none;'} ${props.customStyle}`
})

const rootClass = computed(() => {
  return cn('wd-popup fixed max-h-full overflow-y-auto bg-white ', {
    'wd-popup--center left-1/2 top-1/2 -translate-x-1/2 -translate-y-1/2': props.position === 'center',
    'wd-popup--left left-0 top-0 bottom-0': props.position === 'left',
    'wd-popup--right right-0 top-0 bottom-0': props.position === 'right',
    'wd-popup--top left-0 right-0 top-0': props.position === 'top',
    'wd-popup--bottom left-0 right-0 bottom-0': props.position === 'bottom',
  }, props.customClass, classes.value)
})

onBeforeMount(() => {
  observerTransition()
  if (props.safeAreaInsetBottom) {
    const { safeArea, screenHeight, safeAreaInsets } = uni.getSystemInfoSync()

    if (safeArea) {
      // #ifdef MP-WEIXIN
      safeBottom.value = screenHeight - (safeArea!.bottom || 0)
      // #endif
      // #ifndef MP-WEIXIN
      safeBottom.value = safeAreaInsets ? safeAreaInsets.bottom : 0
      // #endif
    } else {
      safeBottom.value = 0
    }
  }
  if (props.modelValue) {
    enter()
  }
})

watch(
  () => props.modelValue,
  (newVal) => {
    observermodelValue(newVal)
  },
  { deep: true, immediate: true }
)

watch(
  [() => props.position, () => props.transition],
  () => {
    observerTransition()
  },
  { deep: true, immediate: true }
)

function observermodelValue(value: boolean) {
  value ? enter() : leave()
}

function enter() {
  const classNames = getClassNames(props.transition || props.position)
  const duration = props.transition === 'none' ? 0 : isObj(props.duration) ? (props.duration as any).enter : props.duration
  status.value = 'enter'
  emit('before-enter')

  requestAnimationFrame(() => {
    emit('enter')
    classes.value = classNames.enter
    currentDuration.value = duration
    requestAnimationFrame(() => {
      inited.value = true
      display.value = true
      requestAnimationFrame(() => {
        transitionEnded.value = false
        classes.value = classNames['enter-to']
      })
    })
  })
}
function leave() {
  if (!display.value) return
  const classNames = getClassNames(props.transition || props.position)
  const duration = props.transition === 'none' ? 0 : isObj(props.duration) ? (props.duration as any).leave : props.duration
  status.value = 'leave'
  emit('before-leave')

  requestAnimationFrame(() => {
    emit('leave')
    classes.value = classNames.leave
    currentDuration.value = duration

    requestAnimationFrame(() => {
      transitionEnded.value = false
      const timer = setTimeout(() => {
        onTransitionEnd()
        clearTimeout(timer)
      }, currentDuration.value)
      classes.value = classNames['leave-to']
    })
  })
}

function onTransitionEnd() {
  if (transitionEnded.value) return

  transitionEnded.value = true
  if (status.value === 'leave') {
    // 离开后触发
    emit('after-leave')
  } else if (status.value === 'enter') {
    // 进入后触发
    emit('after-enter')
  }
  if (!props.modelValue && display.value) {
    display.value = false
  }
}

function observerTransition() {
  const { transition, position } = props
  name.value = transition || position
}

function handleClickModal() {
  emit('click-modal')
  if (props.closeOnClickModal) {
    close()
  }
}

function close() {
  emit('close')
  emit('update:modelValue', false)
}
function noop() { }
</script>
<style lang="scss" scoped>
@import './index.scss';
</style>
