<template>
  <wd-overlay v-if="cover" :z-index="zIndex" lock-scroll :show="show"
    custom-style="background-color: transparent;pointer-events: auto;"></wd-overlay>
  <wd-transition name="fade" :show="show" :custom-style="transitionStyle" @after-enter="handleAfterEnter"
    @after-leave="handleAfterLeave">
    <view :class="cn(
      'wd-toast inline-block max-w-[300px] py-4 px-5 rounded-lg text-white transition-all duration-200 text-sm box-border shadow-[0px_6px_16px_0px_rgba(0,0,0,.08)]',
      customClass,
      {
        'wd-toast--top translate-y-[-40vh]': position === 'top',
        'wd-toast--middle translate-y-[-18.8vh]': position === 'middle',
        'wd-toast--bottom translate-y-[40vh]': position === 'bottom',
        'wd-toast--with-icon min-w-[150px] inline-flex items-center': (iconName !== 'loading' || msg) && (iconName || iconClass),
        'wd-toast--loading min-w-[auto] p-2.5': iconName === 'loading' && !msg
      })">
      <!--iconName优先级更高-->
      <wd-loading v-if="iconName === 'loading'" :type="loadingType" :color="loadingColor" :size="loadingSize"
        custom-class="wd-toast__icon inline-block mr-3 text-[32px]" />
      <view class="wd-toast__iconWrap leading-[0] vertical-middle wd-toast__icon inline-block mr-3 text-[32px]"
        v-else-if="iconName === 'success' || iconName === 'warning' || iconName === 'info' || iconName === 'error'">
        <view class="wd-toast__iconBox block w-full h-full">
          <view class="wd-toast__iconSvg w-8 h-8 bg-cover bg-no-repeat" :style="svgStyle"></view>
        </view>
      </view>
      <wd-icon v-else-if="iconClass" custom-class="wd-toast__icon inline-block mr-3 text-[32px]" :size="iconSize"
        :class-prefix="classPrefix" :name="iconClass"></wd-icon>
      <!--文本-->
      <view v-if="msg" class="wd-toast__msg text-sm text-left">{{ msg }}</view>
    </view>
  </wd-transition>
</template>

<script lang="ts">
export default {
  name: 'wd-toast',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import wdLoading from '../wd-loading/wd-loading.vue'
import wdOverlay from '../wd-overlay/wd-overlay.vue'
import wdTransition from '../wd-transition/wd-transition.vue'

import { computed, inject, onBeforeMount, ref, watch, type CSSProperties } from 'vue'
import base64 from '../common/base64'
import { defaultOptions, toastDefaultOptionKey, toastIcon } from '.'
import { toastProps, type ToastLoadingType, type ToastOptions } from './types'
import { addUnit, isDef, isFunction, objToStyle } from '../common/util'
import { cn } from '../common/cn'

const props = defineProps(toastProps)

const iconName = ref<string>('') // 图标类型
const msg = ref<string>('') // 消息内容
const position = ref<string>('middle')
const show = ref<boolean>(false)
const zIndex = ref<number>(100)
const loadingType = ref<ToastLoadingType>('outline')
const loadingColor = ref<string>('#4D80F0')
const iconSize = ref<string>() // 图标大小
const loadingSize = ref<string>() // loading大小
const svgStr = ref<string>('') // 图标
const cover = ref<boolean>(false) // 是否存在遮罩层
const classPrefix = ref<string>('wd-icon') // 图标前缀
const iconClass = ref<string>('') // 图标类名

let opened: (() => void) | null = null

let closed: (() => void) | null = null

const toastOptionKey = props.selector ? toastDefaultOptionKey + props.selector : toastDefaultOptionKey
const toastOption = inject(toastOptionKey, ref<ToastOptions>(defaultOptions)) // toast选项

// 监听options变化展示
watch(
  () => toastOption.value,
  (newVal: ToastOptions) => {
    reset(newVal)
  },
  {
    deep: true,
    immediate: true
  }
)

// 监听options变化展示
watch(
  () => iconName.value,
  () => {
    buildSvg()
  },
  {
    deep: true,
    immediate: true
  }
)

/**
 * 动画自定义样式
 */
const transitionStyle = computed(() => {
  const style: CSSProperties = {
    'z-index': zIndex.value,
    position: 'fixed',
    top: '50%',
    left: 0,
    width: '100%',
    transform: 'translate(0, -50%)',
    'text-align': 'center'
  }
  return objToStyle(style)
})

const svgStyle = computed(() => {
  const style: CSSProperties = {
    backgroundImage: `url(${svgStr.value})`
  }
  if (isDef(iconSize.value)) {
    style.width = iconSize.value
    style.height = iconSize.value
  }
  return objToStyle(style)
})

onBeforeMount(() => {
  buildSvg()
})

function handleAfterEnter() {
  if (isFunction(opened)) {
    opened()
  }
}

function handleAfterLeave() {
  if (isFunction(closed)) {
    closed()
  }
}

function buildSvg() {
  if (iconName.value !== 'success' && iconName.value !== 'warning' && iconName.value !== 'info' && iconName.value !== 'error') return
  const iconSvg = toastIcon[iconName.value]()
  const iconSvgStr = `"data:image/svg+xml;base64,${base64(iconSvg)}"`
  svgStr.value = iconSvgStr
}

/**
 * 重置toast选项值
 * @param option toast选项值
 */
function reset(option: ToastOptions) {
  if (option) {
    show.value = isDef(option.show) ? option.show : false

    if (show.value) {
      iconName.value = isDef(option.iconName!) ? option.iconName! : ''
      iconClass.value = isDef(option.iconClass!) ? option.iconClass! : ''
      msg.value = isDef(option.msg!) ? option.msg! : ''
      position.value = isDef(option.position!) ? option.position! : 'middle'
      zIndex.value = isDef(option.zIndex!) ? option.zIndex! : 100
      loadingType.value = isDef(option.loadingType!) ? option.loadingType! : 'outline'
      loadingColor.value = isDef(option.loadingColor!) ? option.loadingColor! : '#4D80F0'
      iconSize.value = isDef(option.iconSize) ? addUnit(option.iconSize) : option.iconSize
      loadingSize.value = isDef(option.loadingSize) ? addUnit(option.loadingSize) : option.loadingSize
      cover.value = isDef(option.cover!) ? option.cover! : false
      classPrefix.value = isDef(option.classPrefix) ? option.classPrefix : 'wd-icon'
      closed = isFunction(option.closed) ? option.closed : null
      opened = isFunction(option.opened) ? option.opened : null
    }
  }
}
</script>