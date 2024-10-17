<template>
  <view
    :class="cn(`fixed top-0 left-0 z-[999] w-full h-full flex flex-col justify-center items-center bg-black/80`, customClass)"
    :style="customStyle" v-if="showPopup" @click="close">
    <view class="w-full h-[242px] transition-[all_ease_0.3s]" @click.stop="">
      <video class="w-full h-[242px] transition-[all_ease_0.3s]" v-if="previdewVideo.url" :controls="true"
        :poster="previdewVideo.poster" :title="previdewVideo.title" play-btn-position="center" :enableNative="true"
        :src="previdewVideo.url" :enable-progress-gesture="false"></video>
    </view>
    <wd-icon name="close"
      :custom-class="`absolute top-0 right-0 box-border p-3 text-center cursor-pointer text-xl text-white`"
      @click="close" />
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-video-preview',
  options: {
    addGlobalClass: true,
    virtualHost: true,

  }
}
</script>

<script lang="ts" setup>
import wdIcon from '../wd-icon/wd-icon.vue'
import { nextTick, reactive, ref } from 'vue'
import { videoPreviewProps, type PreviewVideo, type VideoPreviewExpose } from './types'
import useLockScroll from '../composables/useLockScroll'
import { cn } from '../common/cn'
defineProps(videoPreviewProps)

const showPopup = ref<boolean>(false)
const previdewVideo = reactive<PreviewVideo>({ url: '', poster: '', title: '' })

function open(video: PreviewVideo) {
  showPopup.value = true
  previdewVideo.url = video.url
  previdewVideo.poster = video.poster
  previdewVideo.title = video.title
}

function close() {
  showPopup.value = false
  nextTick(() => {
    handleClosed()
  })
}

function handleClosed() {
  previdewVideo.url = ''
  previdewVideo.poster = ''
  previdewVideo.title = ''
}

// #ifdef H5
useLockScroll(() => showPopup.value)
// #endif

defineExpose<VideoPreviewExpose>({
  open,
  close
})
</script>