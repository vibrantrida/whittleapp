<template>
  <div id="app" class="bg-gray-100 h-screen">
    <div style="-webkit-app-region: drag;" class="absolute w-full p-12"></div>
    <Dropzone></Dropzone>
  </div>
</template>

<script>
import Dropzone from './components/Dropzone.vue'

export default {
  name: 'app',
  components: {
    Dropzone
  }
}
</script>

<style src="./assets/main.css" />
