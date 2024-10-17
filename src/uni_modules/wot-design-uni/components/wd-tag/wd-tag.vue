<template>
  <view
    :class="cn(`wd-tag text-[10px] inline-block text-white py-0 px-0.5 rounded-sm transition-[opacity_0.3s] align-middle leading-[initial] ${customClass} ${tagClass}`)"
    :style="rootStyle" @click="handleClick">
    <view v-if="useIconSlot" class="wd-tag__icon inline-block mr-1 text-xs leading-[1.2] align-baseline">
      <slot name="icon" />
    </view>
    <wd-icon v-else-if="icon" :name="icon"
      custom-class="wd-tag__icon inline-block mr-1 text-xs leading-[1.2] align-baseline" />
    <view class="wd-tag__text inline-block align-text-top" :style="textStyle">
      <slot />
    </view>
    <view
      class="wd-tag__close inline-block ml-6 -mr-1 text-sm h-3.5 leading-[1.1] align-text-bottom text-[#585858] active:text-black/45"
      v-if="closable && round" @click.stop="handleClose">
      <wd-icon name="error-fill" />
    </view>
    <input v-if="dynamicInput && dynamic"
      class="wd-tag__add-text w-[60px] h-3.5 min-h-[14px] inline-block text-xs align-middle p-0"
      :placeholder="translate('placeholder')" type="text" :focus="true" v-model="dynamicValue" @blur="handleBlur"
      @confirm="handleConfirm" />
    <view v-else-if="dynamic" class="wd-tag__text inline-block align-text-top" :style="textStyle"
      @click.stop="handleAdd">
      <slot name="add" v-if="$slots.add"></slot>
      <template v-else>
        <wd-icon name="add"
          custom-class="wd-tag__add align-bottom wd-tag__icon inline-block mr-1 text-xs leading-[1.2] align-baseline" />
        <text>{{ translate('add') }}</text>
      </template>
    </view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-tag',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>
<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import { objToStyle } from '../common/util'
import { computed, ref, watch } from 'vue'
import { useTranslate } from '../composables/useTranslate'
import { tagProps } from './types'
import { cn } from '../common/cn'

const props = defineProps(tagProps)
const emit = defineEmits(['click', 'close', 'confirm'])

const { translate } = useTranslate('tag')

const tagClass = ref<string>('')
const dynamicValue = ref<string>('')
const dynamicInput = ref<boolean>(false)

watch(
  [() => props.useIconSlot, () => props.icon, () => props.plain, () => props.dynamic, () => props.round, () => props.mark],
  () => {
    computeTagClass()
  },
  { deep: true, immediate: true }
)

watch(
  () => props.type,
  (newValue) => {
    if (!newValue) return
    // type: 'primary', 'danger', 'warning', 'success'
    const type = ['primary', 'danger', 'warning', 'success', 'default']
    if (type.indexOf(newValue) === -1) console.error(`type must be one of ${type.toString()}`)
    computeTagClass()
  },
  { deep: true, immediate: true }
)

watch(
  () => dynamicInput.value,
  () => {
    computeTagClass()
  },
  { deep: true, immediate: true }
)

const rootStyle = computed(() => {
  const rootStyle: Record<string, any> = {}
  if (!props.plain && props.bgColor) {
    rootStyle['background'] = props.bgColor
  }
  if (props.bgColor) {
    rootStyle['border-color'] = props.bgColor
  }
  return `${objToStyle(rootStyle)};${props.customStyle}`
})

const textStyle = computed(() => {
  const textStyle: Record<string, any> = {}
  if (props.color) {
    textStyle['color'] = props.color
  }
  return objToStyle(textStyle)
})

function computeTagClass() {
  const { type, plain, round, mark, dynamic, icon, useIconSlot } = props
  let tagClassList: string[] = []
  if (type === 'default') {
    tagClassList.push('bg-[linear-gradient(49deg,#808080_0%,#999999_100%)]')
    if (plain) {
      tagClassList.push('bg-transparent border border-[#585858] border-solid text-[#585858] py-0 px-1')
    }
    if (round) {
      tagClassList.push('leading-[1.2] text-xs py-1 px-3 bg-transparent border border-[rgb(225,225,225)] border-solid text-[rgb(102,102,102)] rounded-xl')
    }
    if (mark) {
      tagClassList.push('py-[1px] px-1.5 rounded-[6px_2px_6px_2px]')
    }
    if (mark && plain) {
      tagClassList.push('py-0 px-1.5')
    }
  }
  if (type === 'primary') {
    tagClassList.push('bg-primary')
    if (plain) {
      tagClassList.push('bg-transparent border border-primary border-solid text-primary py-0 px-1')
    }
    if (round) {
      tagClassList.push('leading-[1.2] text-xs py-1 px-[11px] bg-transparent border border-primary border-solid text-primary rounded-xl')
    }
    if (mark) {
      tagClassList.push('py-[1px] px-1.5 rounded-[6px_2px_6px_2px]')
    }
    if (mark && plain) {
      tagClassList.push('py-0 px-1.5')
    }
  }
  if (type === 'danger') {
    tagClassList.push('bg-danger')
    if (plain) {
      tagClassList.push('bg-transparent border border-danger border-solid text-danger py-0 px-1')
    }
    if (round) {
      tagClassList.push('leading-[1.2] text-xs py-1 px-[11px] bg-transparent border border-danger border-solid text-danger rounded-xl')
    }
    if (mark) {
      tagClassList.push('py-[1px] px-1.5 rounded-[6px_2px_6px_2px]')
    }
    if (mark && plain) {
      tagClassList.push('py-0 px-1.5')
    }
  }
  if (type === 'warning') {
    tagClassList.push('bg-warning')
    if (plain) {
      tagClassList.push('bg-transparent border border-warning border-solid text-warning py-0 px-1')
    }
    if (round) {
      tagClassList.push('leading-[1.2] text-xs py-1 px-[11px] bg-transparent border border-warning border-solid text-warning rounded-xl')
    }
    if (mark) {
      tagClassList.push('py-[1px] px-1.5 rounded-[6px_2px_6px_2px]')
    }
    if (mark && plain) {
      tagClassList.push('py-0 px-1.5')
    }
  }
  if (type === 'success') {
    tagClassList.push('bg-success')
    if (plain) {
      tagClassList.push('bg-transparent border border-success border-solid text-success py-0 px-1')
    }
    if (round) {
      tagClassList.push('leading-[1.2] text-xs py-1 px-[11px] bg-transparent border border-success border-solid text-success rounded-xl')
    }
    if (mark) {
      tagClassList.push('py-[1px] px-1.5 rounded-[6px_2px_6px_2px]')
    }
    if (mark && plain) {
      tagClassList.push('py-0 px-1.5')
    }
  }
  type && tagClassList.push(`is-${type}`)
  plain && tagClassList.push('is-plain')
  round && tagClassList.push('is-round')
  mark && tagClassList.push('is-mark')
  dynamicInput.value && tagClassList.push('is-dynamic-input border-primary')
  if (icon || useIconSlot) tagClassList.push('is-icon text-xs leading-[1.2] py-0.5 px-[5px]')
  tagClass.value = tagClassList.join(' ')
}

function handleClick(event: any) {
  emit('click', event)
}
function handleClose(event: any) {
  emit('close', event)
}
function handleAdd() {
  dynamicInput.value = true
  dynamicValue.value = ''
}
function handleBlur() {
  setDynamicInput()
}
function handleConfirm(event: any) {
  setDynamicInput()
  emit('confirm', {
    value: event.detail.value
  })
}
function setDynamicInput() {
  dynamicInput.value = false
}
</script>