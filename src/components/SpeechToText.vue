<template>
  <div class="speech-to-text">
    <span :class="open ? 'icon active' : 'icon'" @click="handleClick">
      <i v-if="!open" class="fas fa-microphone-slash"></i>
      <i v-else class="fas fa-microphone"></i>
    </span>
    <ul class="chat">
      <li class="text-other">
        Chịu sao thấu híc híc
      </li>
      <li class="text-me">
        Say that you like you use faUser icon of both the kind regular and
        solid, then do this change
      </li>
      <li class="text-other">
        Chịu sao thấu híc híc
      </li>
      <li class="text-me">
        I had the same issue and the solution was to follow the official docs.
      </li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
export default defineComponent({
  name: 'SpeechToText',
  setup() {
    const open = ref(false)

    const handleClick = () => {
      open.value = !open.value
    }

    return {
      open,
      handleClick,
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
