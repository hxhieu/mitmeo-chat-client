<template>
  <router-view />
</template>

<style lang="scss">
@import './scss/bulma';
@import './scss/mixins';

// FontAwesome
$fa-font-path: '~@fortawesome/fontawesome-free/webfonts';
@import '~@fortawesome/fontawesome-free/scss/fontawesome.scss';
@import '~@fortawesome/fontawesome-free/scss/solid.scss';

#app {
  @include full-screen;
}
</style>
