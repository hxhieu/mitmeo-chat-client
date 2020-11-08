<template>
  <div id="home">
    <div id="remote-video" ref="remoteVideo"></div>
    <div
      id="local-video"
      ref="localVideo"
      :style="{ width: `${previewWidth}px`, height: `${previewHeight}px` }"
    ></div>
    <div id="speech-to-text">
      <SpeechToText />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import {
  connect,
  createLocalVideoTrack,
  Participant,
  RemoteParticipant,
  RemoteVideoTrack,
  Track,
} from 'twilio-video'
import SpeechToText from '@/components/SpeechToText.vue'

export default defineComponent({
  name: 'Home',
  components: {
    SpeechToText,
  },
  setup() {
    const previewWidth = ref(400)
    const previewHeight = ref(300)
    const remoteVideo = ref<HTMLDivElement>()
    const localVideo = ref<HTMLDivElement>()

    const attachRemoteTracks = (participant: RemoteParticipant) => {
      participant.tracks.forEach(publication => {
        if (publication.isSubscribed) {
          const { track } = publication
          remoteVideo.value?.appendChild((track as RemoteVideoTrack)?.attach())
        }
      })

      participant.on('trackSubscribed', (track: Track) => {
        remoteVideo.value?.appendChild((track as RemoteVideoTrack)?.attach())
      })
    }

    createLocalVideoTrack().then(track => {
      localVideo.value?.appendChild(track.attach())
    })

    connect(process.env[process.env.VUE_APP_TWILIO || ''] as string, {
      name: 'mit-meo-chat',
      audio: false,
      video: {
        width: 800,
      },
    }).then(
      room => {
        console.log(`Connected to Room: ${room.name}`)
        // Existing participants
        room.participants.forEach(participant => {
          attachRemoteTracks(participant)
        })
        // Future connecting participants
        room.on('participantConnected', (participant: RemoteParticipant) => {
          console.log(`Participant "${participant.identity}" connected`)
          attachRemoteTracks(participant)
        })
      },
      error => {
        console.error(`Unable to connect to Room: ${error.message}`)
      },
    )

    return {
      localVideo,
      remoteVideo,
      previewWidth,
      previewHeight,
    }
  },
})
</script>

<style lang="scss">
@import '../scss/_mixins';
#home {
  @include full-screen;

  #remote-video {
    height: 100%;
    text-align: center;
    video {
      height: 100%;
    }
  }

  #local-video {
    position: absolute;
    left: 20px;
    bottom: 20px;
    z-index: 100;
  }

  #speech-to-text {
    position: absolute;
    right: 0;
    top: 0;
    bottom: 0;
    left: 0;
    z-index: 200;
  }
}
</style>
