<template>
  <div id="home">
    <div id="focus-video">
      <Video>
        <img src="../assets/andrew-small-EfhCUc_fjrU-unsplash.jpg" />
      </Video>
    </div>
    <div
      id="preview-video"
      :style="{ width: `${previewWidth}px`, height: `${previewHeight}px` }"
    >
      <Video>
        <img src="../assets/abel-y-costa-jb-SMviXCjI-unsplash.jpg" />
      </Video>
    </div>
    <div id="speech-to-text">
      <SpeechToText />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import Video from '@/components/Video.vue'
import SpeechToText from '@/components/SpeechToText.vue'

export default defineComponent({
  name: 'Home',
  components: {
    Video,
    SpeechToText,
  },
  setup() {
    const previewWidth = ref(400)
    const previewHeight = ref(300)

    return {
      previewWidth,
      previewHeight,
    }
  },
})
</script>

<style lang="scss" scoped>
@import '../scss/_mixins';
#home {
  @include full-screen;

  #preview-video {
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
