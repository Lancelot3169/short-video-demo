
<template>
  <main>
    <video ref="videoRef" class="video-js" 
    playsinline
    webkit-playsinline
    x5-video-player-type
    :class="{'vid-portrait': videoOrientation === 2}"
    ></video>
  </main>
</template>
<script setup>
import 'video.js/dist/video-js.min.css'
import videojs from 'video.js'
import { nextTick, onMounted, onUnmounted, ref, watch } from 'vue'
import { useRoute } from 'vue-router'

const route = useRoute()
const videoRef = ref(null)
const videoOrientation = ref(-1)
let player = null
const initVideo = () => {
  const defaultSource = 'https://video-new.aisports.io/sv/479826bf-18f2e9f92cc/479826bf-18f2e9f92cc.mp4'
  const vidUrl = route?.query?.source || defaultSource
  const options = {
    preload: 'auto',
    width: '100%',
    height: '100%',
    loop: true,
    autoplay: true,
    muted: true,
    controls: false,
    fluid: true,
    bigPlayButton: false,
    loadingSpinner: false,
    errorDisplay: false,
    sources: [
      {
        src: vidUrl,
        // type: 'application/x-mpegURL'
        type: 'video/mp4'
      }
    ]
  }
  nextTick(async () => {
    console.log('next tick')
    if (!player) {
      player = videojs(videoRef.value, options)
    }

    player.on('loadedmetadata', () => {
      const videoWidth = player.videoWidth();
      const videoHeight = player.videoHeight();

      if (videoWidth > videoHeight) {
        console.log('影片是橫向的')
        videoOrientation.value = 1
      } else if (videoWidth < videoHeight) {
        console.log('影片是直向的')
        videoOrientation.value = 2
      } else {
        console.log('影片是正方形的')
        videoOrientation.value = 3
      }
    })

    // player.on('waiting', () => {
    //   videoWaiting.value = true
    //   videoError.value = false
    //   videoPause.value = false
    // })

    // player.on('playing', () => {
    //   videoWaiting.value = false
    //   videoError.value = false
    //   videoPause.value = false
    // })

    // player.on('error', () => {
    //   videoError.value = true
    //   videoWaiting.value = false
    // })
    // player.on('pause', () => {
    //   videoPause.value = true
    // })
    // player.on('click', () => {
    //   pauseHandle()
    // })
  })
}
onMounted(() => {
  initVideo()
})
</script>
<style lang="scss" scoped>
.video-js {
  position: relative;
  z-index: 8;
  width: 100% !important;
  height: 100% !important;
  margin: auto;
  padding: 0 !important;
  object-fit: contain;

  video {
    position: relative;
    z-index: -1;
  }

  &.vid-portrait {
    object-fit: cover;
  }
}
</style>