<template>
  <view :class="['wd-loadmore w-full h-12 leading-[48px] text-center text-black/45', customClass]" :style="customStyle"
    @click="reload">
    <wd-divider v-if="state === 'finished'">{{ finishedText || translate('finished') }}</wd-divider>
    <block v-if="state === 'error'">
      <text class="wd-loadmore__text inline-block align-middle text-sm">{{ errorText || translate('error') }}</text>
      <text class="wd-loadmore__text inline-block align-middle text-sm is-light my-0 mx-1.5 text-primary">{{
        translate('retry')
      }}</text>
      <wd-icon name="refresh" custom-class="wd-loadmore__refresh inline-block align-middle text-base text-primary" />
    </block>
    <block v-if="state === 'loading'">
      <wd-loading custom-class="wd-loadmore__loading inline-block mr-2 align-middle w-4 h-4" />
      <text class="wd-loadmore__text inline-block align-middle text-sm">{{ loadingText || translate('loading') }}</text>
    </block>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-loadmore',
  options: {
    virtualHost: true,
    addGlobalClass: true,

  }
}
</script>

<script lang="ts" setup>
import wdDivider from '../wd-divider/wd-divider.vue'
import wdLoading from '../wd-loading/wd-loading.vue'
import wdIcon from '../wd-icon/wd-icon.vue'
import { ref } from 'vue'
import { useTranslate } from '../composables/useTranslate'
import { loadmoreProps, type LoadMoreState } from './types'

const props = defineProps(loadmoreProps)
const emit = defineEmits(['reload'])

const { translate } = useTranslate('loadmore')

const currentState = ref<LoadMoreState | null>(null)

function reload() {
  if (props.state !== 'error') return
  currentState.value = 'loading'
  emit('reload')
}
</script>
