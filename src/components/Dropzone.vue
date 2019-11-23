<template>
  <div class="flex flex-wrap w-full h-full items-center justify-center align-middle">
    <p class="text-gray-600 font-semibold pt-4">Drop your comic strip below.</p>
    <div class="flex flex-col w-full h-auto items-center justify-center">
      <div @dragover.prevent @drop="drop($event)" class="bg-gray-200 border-2 border-gray-500 border-dashed rounded w-5/6 h-56 p-4">
        <div class="flex flex-col h-full items-center justify-center">
          <p class="text-gray-500 text-center">{{ fileName }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'dropzone',
  props: {
    fileName: String
  },
  methods: {
    drop: function (e) {
      e.preventDefault()

      let items = e.dataTransfer.items
      this.fileName = items[0].getAsFile().name
    }
  }
}
</script>
