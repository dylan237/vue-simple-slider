<template>
  <div id="app">
    <router-view />
  </div>
</template>

<style lang="scss">
@import '@/assets/scss/main.scss';

span {
  @include color-contrast('dylan', true);
  font-size: 50px;
}
</style>
