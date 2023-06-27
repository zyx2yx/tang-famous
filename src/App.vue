<script setup>
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
import TangDynasty from './components/TangDynasty.vue'
</script>

<template>
  <!-- <header>
    <img alt="Vue logo" class="logo" src="@/assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />

      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
    </div>
  </header> -->

  <!-- <RouterView /> -->
  <div id="tooltip" class="prompt-hide"></div>
  <TangDynasty />
</template>

<style scoped>
/* 提示框样式 */
#tooltip {
  font: 10px sans-serif;
}

.prompt-hide {
  display: none;
  pointer-events: none;
}

.prompt-show {
  display: float;
  color: black;
  position: absolute;
  border: 1px solid black;
  z-index: 100;
  background-color: white;
}
/* 提示框样式 */
</style>
