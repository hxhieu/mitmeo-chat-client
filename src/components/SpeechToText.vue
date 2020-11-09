<template>
  <div class="speech-to-text">
    <span :class="open ? 'icon active' : 'icon'" @click="open = !open">
      <i v-if="!open" class="fas fa-microphone-slash"></i>
      <i v-else class="fas fa-microphone"></i>
    </span>
    <ul class="chat">
      <li v-for="(text, idx) in results" :key="idx" class="text-me">
        {{ text }}
      </li>
    </ul>
    <audio ref="testRecording" controls autoplay playsinline></audio>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, onMounted, watch, watchEffect } from 'vue'
import { v2 as GoogleTranslate } from '@google-cloud/translate'
import RecordRTC, { Options, StereoAudioRecorder } from 'recordrtc'
import hark from 'hark'

const isEdge =
  navigator.userAgent.indexOf('Edge') !== -1 &&
  (!!navigator.msSaveOrOpenBlob || !!navigator.msSaveBlob)
const isSafari = /^((?!chrome|android).)*safari/i.test(navigator.userAgent)

let recorder: RecordRTC | null

export default defineComponent({
  name: 'SpeechToText',
  setup() {
    const open = ref(false)
    const results = ref<string[]>([])
    const testRecording = ref<HTMLAudioElement>()

    onMounted(() => {
      const translate = new GoogleTranslate.Translate({
        key: process.env.VUE_APP_GOOGLE_API_KEY,
      })

      if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
        alert('This browser does not supports WebRTC getUserMedia API.')
      }

      navigator.mediaDevices
        .getUserMedia({
          audio: isEdge
            ? true
            : {
                echoCancellation: false,
              },
        })
        .then(stream => {
          const options: Options = {
            type: 'audio',
            numberOfAudioChannels: isEdge ? 1 : 2,
            checkForInactiveTracks: true,
            bufferSize: 16384,
          }

          if (isSafari || isEdge) {
            options.recorderType = StereoAudioRecorder
          }

          if (
            navigator.platform &&
            navigator.platform
              .toString()
              .toLowerCase()
              .indexOf('win') === -1
          ) {
            options.sampleRate = 48000 // or 44100 or remove this line for default
          }

          if (isSafari) {
            options.sampleRate = 44100
            options.bufferSize = 4096
            options.numberOfAudioChannels = 2
          }

          if (recorder) {
            recorder.destroy()
            recorder = null
          }
          recorder = new RecordRTC(stream, options)
          const speechEvents = hark(stream)
          speechEvents.on('speaking', () => {
            console.log('speaking')
          })
          speechEvents.on('stopped_speaking', () => {
            console.log('stop speaking')
          })
        })
        .catch(error => {
          alert('Unable to capture your microphone. Please check console logs.')
          console.error(error)
        })
    })

    watch(
      () => open.value,
      isOpen => {
        console.log(isOpen)
        if (isOpen) {
          recorder?.startRecording()
        } else {
          recorder?.stopRecording(() => {
            const blob = recorder?.getBlob()
            if (testRecording.value && blob) {
              testRecording.value.src = URL.createObjectURL(blob)
            }
          })
        }
      },
    )

    return {
      open,
      results,
      testRecording,
    }
  },
})
</script>

<style lang="scss" scoped>
@import '../scss/mixins';

$primary-colour: rgb(101, 0, 126);
$secondary-colour: rgb(219, 106, 0);
$bubble-margin: 200px;

@keyframes open-active {
  0% {
    background-color: $primary-colour;
  }
  50% {
    background-color: lighten($primary-colour, 10);
  }
  100% {
    background-color: $primary-colour;
  }
}

.speech-to-text {
  @include full-screen;
  display: flex;
  flex-direction: column-reverse;

  .chat {
    font-size: 4rem;
    z-index: 1;
    pointer-events: none;
    text-shadow: 2px 2px 3px #000;
    color: #f5f5f5;
    li {
      display: block;
      padding: 20px 40px;
      margin-top: 20px;
      &.text-me {
        // color: lighten($primary-colour, 20);
        margin-left: $bubble-margin;
        background: rgba($color: $primary-colour, $alpha: 0.25);
        border-radius: 20px 0 0 20px;
        .sub {
          color: $primary-colour;
        }
      }
      &.text-other {
        // color: $secondary-colour;
        margin-right: $bubble-margin;
        background: rgba($color: $secondary-colour, $alpha: 0.25);
        border-radius: 0 20px 20px 0;
        .sub {
          color: $secondary-colour;
        }
      }
    }
  }

  .icon {
    display: inline-block;
    background: $primary-colour;
    color: #fff;
    border-radius: 100%;
    position: absolute;
    bottom: 20px;
    right: 20px;
    text-align: center;
    i {
      margin: 40px 35px;
      font-size: 40px;
      width: 50px;
      height: 40px;
    }
    &:hover:not(.active) {
      background: #fff;
      i {
        color: #333;
      }
    }

    &.active {
      animation: open-active 1s infinite;
    }

    transition: all 0.25s;
    cursor: pointer;
  }
}
</style>
