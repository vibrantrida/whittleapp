<template>
  <div class="flex flex-col justify-around items-center m-auto" style="width: calc(100% - 32px); height: calc(100% - 32px);">
    <div @click="changeState" class="flex flex-col justify-center items-stretch py-10 w-full h-full border-2 border-gray-300 hover:border-gray-400 border-dashed hover:border-solid rounded">
      <Message :state="state" />
    </div>
  </div>
</template>

<script>
import Message from './Message.vue'

export default {
  name: 'Processor',
  components: {
    Message
  },
  data () {
    return {
      state: 0
    }
  },
  methods: {
    changeState () {
      if (this.state >= 3) {
        this.state = 0
      } else {
        this.state = this.state + 1
      }
    }
  }
}
</script>
