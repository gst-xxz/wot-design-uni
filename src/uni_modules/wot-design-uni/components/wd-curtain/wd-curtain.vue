<template>
  <view>
    <wd-popup v-model="show" transition="zoom-in" position="center" :close-on-click-modal="closeOnClickModal"
      :hide-when-close="hideWhenClose" @before-enter="beforeenter" @enter="enter" @after-enter="afterenter"
      @before-leave="beforeleave" @leave="leave" @after-leave="afterleave" @close="close" @click-modal="clickModal"
      :custom-class="cn(`wd-curtain inline-block rounded-3xl overflow-y-visible bg-transparent text-[0] ${customClass}`)"
      :custom-style="customStyle">
      <view class="wd-curtain__content relative inline-block bg-transparent rounded-3xl">
        <image :src="src" class="wd-curtain__content-img block w-auto h-auto rounded-3xl" :style="imgStyle"
          @click="clickImage" @error="imgErr" @load="imgLoad"></image>
        <wd-icon name="close-outline" :custom-class="cn(`wd-curtain__content-close absolute top-2.5 right-2.5 m-0 p-1.5 text-white text-3xl tap-transparent`, {
          'm-0 ml-[-18px] top-[-62px] right-[unset] left-1/2 bottom-[unset]': closePosition === 'top',
          'm-0 ml-[-18px] top-[-62px] right-[unset] -left-1.5 bottom-[unset]': closePosition === 'top-left',
          'm-0 ml-[-18px] top-[-62px] -right-1.5 left-[unset] bottom-[unset]': closePosition === 'top-right',
          'm-0 ml-[-18px] top-[unset] right-[unset] left-[unset] bottom-[-62px]': closePosition === 'bottom',
          'm-0 top-[unset] right-[unset] -left-1.5 bottom-[-62px]': closePosition === 'bottom-left',
          'm-0 top-[unset] right-[-6px] -left-1.5 bottom-[-62px]': closePosition === 'bottom-right',
        })" @click="close" class="right-unset " />
      </view>
    </wd-popup>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-curtain',
  options: {
    virtualHost: true,
    addGlobalClass: true,

  }
}
</script>

<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import wdPopup from '../wd-popup/wd-popup.vue'
import { ref, watch } from 'vue'
import { curtainProps } from './types'
import { cn } from '../common/cn'
const props = defineProps(curtainProps)

const emit = defineEmits([
  'beforeenter',
  'enter',
  'afterenter',
  'beforeleave',
  'leave',
  'afterleave',
  'close',
  'closed',
  'click-modal',
  'load',
  'error',
  'click'
])

const show = ref<boolean>(false)
const imgSucc = ref<boolean>(true)
const imgStyle = ref<string>('')
const imgScale = ref<number>(1)

watch(
  () => props.value,
  () => {
    computedShowImg()
  },
  {
    deep: true,
    immediate: true
  }
)

watch(
  () => props.width,
  () => {
    computeImgStyle()
  },
  {
    deep: true,
    immediate: true
  }
)

function computedShowImg() {
  if (props.value && imgSucc.value) {
    show.value = true
  } else {
    show.value = false
    close()
  }
}

function computeImgStyle() {
  let style = ''
  if (props.width) {
    style += `width: ${props.width}px ;`
    style += `height: ${props.width / imgScale.value}px`
  }
  imgStyle.value = style
}

function beforeenter() {
  emit('beforeenter')
}

function enter() {
  emit('enter')
}
function afterenter() {
  emit('afterenter')
}

function beforeleave() {
  emit('beforeleave')
}

function leave() {
  emit('leave')
}

function afterleave() {
  emit('afterleave')
  emit('closed')
}

function close() {
  show.value = false
  emit('close')
}

function clickModal() {
  emit('click-modal')
}

function imgLoad(event: any) {
  const { height, width } = event.detail
  imgScale.value = width / height
  imgSucc.value = true
  computeImgStyle()
  emit('load')
}
function imgErr() {
  imgSucc.value = false
  emit('error')
}

function clickImage() {
  if (props.to) {
    uni.navigateTo({
      url: props.to
    })
  }
  emit('click')
  close()
}
</script>
