<template>
  <div class="text-center align-middle">
    <h1 class="font-medium text-2xl text-gray-800">{{ text }}</h1>
    <p class="tracking-tight text-md text-gray-600">{{ subtext }}</p>
  </div>
</template>

<script>
export default {
  name: 'Message',
  props: {
    state: Number
  },
  data () {
    return {
      text: 'Drag & drop',
      subtext: 'your comic strip here'
    }
  },
  watch: {
    state () {
      switch (this.state) {
        case 0: // idle
          this.text = 'Drag & drop'
          this.subtext = 'your comic strip here'
          break
        case 1: // busy
          this.text = 'Working...'
          this.subtext = 'this should take a few seconds'
          break
        case 2: // success
          this.text = 'Hooray!'
          this.subtext = 'your comic strip is ready!'
          break
        case 3: // failure
          this.text = 'Oops!'
          this.subtext = 'something went wrong, try again?'
          break
      }
    }
  }
}
</script>
