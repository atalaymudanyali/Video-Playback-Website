<template>
  <div>
    <video ref="theVideo"
      @play="togglePlayButtonLabel"
      @pause="togglePlayButtonLabel"
      @durationchange="setupSeekbar"
      @timeupdate="updateSeekbar"
    >
      <source src="/bigBunny.mp4" type="video/mp4">
    </video>
    <br>

    <button ref="playButton" @click="togglePlay">{{ playButtonLabel }}</button><br>
    <input type="range" min="0" :max="seekMax" v-model="seekValue" @change="seekVideo"><br>
    <button @click="requestFullscreen">Fullscreen</button><br>
    <button @click="toggleMute">{{ muteButtonText }}</button><br>
    <input type="range" min="0" max="1" step="0.01" v-model="volume">
  </div>
</template>

<script setup lang="ts">
import { ref, watchEffect } from 'vue'

const theVideo = ref<HTMLVideoElement|null>(null)
const playButton = ref<HTMLButtonElement|null>(null)

const seekMax = ref<number>(0)
const seekValue = ref<number>(0)

const volume = ref<number>(1)
const isMuted = ref<boolean>(false)

function togglePlay(): void {
  if (theVideo.value!.paused || theVideo.value!.ended) {
    theVideo.value!.play()
  } else {
    theVideo.value!.pause()
  }
}

function togglePlayButtonLabel(): void {
  playButton.value!.textContent = theVideo.value!.paused ? "Play" : "Pause"
}

function setupSeekbar(): void {
  seekMax.value = theVideo.value!.duration
}

function updateSeekbar(): void {
  seekValue.value = theVideo.value!.currentTime
}

function seekVideo(): void  {
  theVideo.value!.currentTime = seekValue.value
}

function requestFullscreen(): void {
  theVideo.value!.requestFullscreen()
}

function toggleMute(): void {
  isMuted.value = !isMuted.value
  theVideo.value!.muted = isMuted.value
}

const muteButtonText = ref<string>("Mute");
const playButtonLabel = ref<string>("Play");

watchEffect(() => {
  muteButtonText.value = isMuted.value ? "Unmute" : "Mute";
})

// Listen to changes in the volume slider and update the video volume accordingly
watchEffect(() => {
  if (theVideo.value) {
    theVideo.value.volume = volume.value;
  }
});
</script>

<style scoped>

</style>
