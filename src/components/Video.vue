<template>
  <div>
    <slot></slot>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
export default defineComponent({
  name: 'Video',
})
</script>

<style lang="scss" scoped>
@import '../scss/mixins';
.video {
  @include full-screen;
  background: #000;
}
</style>
