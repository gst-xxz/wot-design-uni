<template>
  <view :class="themeClass" :style="themeStyle">
    <slot />
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-config-provider',
  options: {
    virtualHost: true,
    addGlobalClass: true,

  }
}
</script>

<script lang="ts" setup>
import { computed } from 'vue'
import { configProviderProps } from './types'
import { objToStyle } from '../common/util'
import { cn } from '../common/cn'
const props = defineProps(configProviderProps)

const themeClass = computed(() => {
  return cn(`wot-theme-${props.theme} ${props.customClass}`)
})

const themeStyle = computed(() => {
  const styleObj = mapThemeVarsToCSSVars(props.themeVars)
  return styleObj ? `${objToStyle(styleObj)};${props.customStyle}` : props.customStyle
})

const kebabCase = (str: string): string => {
  str = str.replace(str.charAt(0), str.charAt(0).toLocaleLowerCase())
  return str.replace(/([a-z])([A-Z])/g, (_, p1, p2) => p1 + '-' + p2.toLowerCase())
}


const mapThemeVarsToCSSVars = (themeVars: Record<string, string>) => {
  if (!themeVars) return
  const cssVars: Record<string, string> = {}
  Object.keys(themeVars).forEach((key) => {
    cssVars[`--wot-${kebabCase(key)}`] = themeVars[key]
  })

  return cssVars
}
</script>
