<template>
  <view :class="cn(
    'wd-cell relative pl-[15px] bg-white text-black/85 leading-6 tap-transparent no-underline',
    isBorder ? 'is-border' : '',
    size ? 'is-' + size : '',
    center ? 'is-center' : '',
    customClass
  )" :style="customStyle" :hover-class="isLink || clickable ? 'is-hover bg-black/5' : 'none'" :hover-stay-time="70"
    @click="onClick">
    <view :class="cn(
      'wd-cell__wrapper relative flex justify-between items-start py-2.5 px-[15px]',
      vertical ? 'is-vertical block' : '',
      center ? 'items-center' : '',
      { 'relative': isBorder }
    )">
      <view :class="cn(
        'wd-cell__left relative flex-1 flex text-left text-sm box-border mr-[15px]',
        isRequired ? `is-required pl-3 after:content-['*'] after:text-danger after:text-lg after:absolute after:top-0 after:left-0` : '',
        vertical ? 'mr-0' : ''
      )" :style="titleWidth ? 'min-width:' + titleWidth + ';max-width:' + titleWidth + ';' : ''">
        <!--左侧icon部位-->
        <wd-icon v-if="icon" :name="icon"
          :custom-class="cn('wd-cell__icon block relative mr-1 text-base h-6', customIconClass)"></wd-icon>
        <slot v-else name="icon" />

        <view class="wd-cell__title flex-1 w-full text-sm">
          <!--title BEGIN-->
          <view v-if="title" :class="customTitleClass">{{ title }}</view>
          <slot v-else name="title"></slot>
          <!--title END-->

          <!--label BEGIN-->
          <view v-if="label"
            :class="cn('wd-cell__label mt-0.5 text-xs text-black/45 overflow-hidden text-ellipsis whitespace-nowrap', customLabelClass)">
            {{ label }}</view>
          <slot v-else name="label" />
          <!--label END-->
        </view>
      </view>
      <!--right content BEGIN-->
      <view :class='cn("wd-cell__right relative flex-1", { "mt-4": vertical })'>
        <view class="wd-cell__body flex">
          <!--文案内容-->
          <view
            :class="cn('wd-cell__value relative flex-1 text-sm text-black/85 text-right align-middle', customValueClass)">
            <slot>{{ value }}</slot>
          </view>
          <!--箭头-->
          <wd-icon v-if="isLink" custom-class="wd-cell__arrow-right block ml-2 w-[18px] text-black/25 h-6 leading-6"
            name="arrow-right" />
          <slot v-else name="right-icon" />
        </view>
        <view v-if="errorMessage" class="wd-cell__error-message text-danger text-xs leading-6 text-left align-middle">{{
          errorMessage }}</view>
      </view>
      <!--right content END-->
    </view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-cell',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import { computed } from 'vue'
import { useCell } from '../composables/useCell'
import { useParent } from '../composables/useParent'
import { FORM_KEY } from '../wd-form/types'
import { cellProps } from './types'
import { isDef } from '../common/util'
import { cn } from '../common/cn'

const props = defineProps(cellProps)
const emit = defineEmits(['click'])

const cell = useCell()

const isBorder = computed(() => {
  return isDef(cell.border.value) ? cell.border.value : props.border
})

const { parent: form } = useParent(FORM_KEY)

const errorMessage = computed(() => {
  if (form && props.prop && form.errorMessages && form.errorMessages[props.prop]) {
    return form.errorMessages[props.prop]
  } else {
    return ''
  }
})

// 是否展示必填
const isRequired = computed(() => {
  let formRequired = false
  if (form && form.props.rules) {
    const rules = form.props.rules
    for (const key in rules) {
      if (Object.prototype.hasOwnProperty.call(rules, key) && key === props.prop && Array.isArray(rules[key])) {
        formRequired = rules[key].some((rule) => rule.required)
      }
    }
  }
  return props.required || props.rules.some((rule) => rule.required) || formRequired
})

/**
 * @description 点击cell的handle
 */
function onClick() {
  const url = props.to

  if (props.clickable || props.isLink) {
    emit('click')
  }
  if (url && props.isLink) {
    if (props.replace) {
      uni.redirectTo({ url })
    } else {
      uni.navigateTo({ url })
    }
  }
}
</script>

<style lang="scss" scoped>
@import './index.scss';
</style>
