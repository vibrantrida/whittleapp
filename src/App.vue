<template>
  <div id="app" class="h-screen">
    <div style="-webkit-app-region: drag;" class="absolute w-full p-6"></div>
    <Tray />
  </div>
</template>

<script>
import Tray from './components/Tray.vue'

export default {
  name: 'app',
  components: {
    Tray
  }
}
</script>

<style src="./assets/main.css" />
