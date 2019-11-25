<template>
  <div class="flex flex-col justify-around items-center m-auto" style="width: calc(100% - 32px); height: calc(100% - 32px);">
    <div @dragover.prevent @drop="drop($event)" class="flex flex-col justify-center items-stretch py-10 w-full h-full border-4 border-gray-300 hover:border-gray-400 border-dashed hover:border-solid rounded">
      <Message :state="state" />
    </div>
  </div>
</template>

<script>
import fs from 'fs'
import path from 'path'
import { shell } from 'electron'

import jimp from 'jimp/dist'

import Message from './Message.vue'

export default {
  name: 'Processor',
  components: {
    Message
  },
  data () {
    return {
      state: 0,
      filePath: ''
    }
  },
  methods: {
    async generateTapas () {
      let maxWidth = 940
      let maxHeight = 4000
      await jimp.read(this.filePath)
        .then(image => {
          if (image.bitmap.width > maxWidth) {
            image.resize(maxWidth, jimp.AUTO)
          }
          if (image.bitmap.height > maxHeight) {
            let sliceCount = Math.ceil(image.bitmap.height / maxHeight)
            let slices = []
            let lastY = 0
            for (let i = 0; i < sliceCount; i++) {
              slices[i] = image.clone()
              slices[i].crop(0, lastY, image.bitmap.width, image.bitmap.height / sliceCount)
              slices[i].write(`${path.dirname(this.filePath) + '/tapas/' + path.basename(this.filePath, path.extname(this.filePath))}_${i}.${path.extname(this.filePath)}`)
              lastY += slices[i].bitmap.height
            }
          } else {
            image.write(`${path.dirname(this.filePath) + '/tapas/' + path.basename(this.filePath)}`)
          }
        })
        .catch(err => {
          if (err) {
            this.state = 3
            console.error(err)
          }
        })
      return true
    },
    async generateWebtoons () {
      let maxWidth = 800
      let maxHeight = 1280
      await jimp.read(this.filePath)
        .then(image => {
          if (image.bitmap.width > maxWidth) {
            image.resize(maxWidth, jimp.AUTO)
          }
          if (image.bitmap.height > maxHeight) {
            let sliceCount = Math.ceil(image.bitmap.height / maxHeight)
            let slices = []
            let lastY = 0
            for (let i = 0; i < sliceCount; i++) {
              slices[i] = image.clone()
              slices[i].crop(0, lastY, image.bitmap.width, image.bitmap.height / sliceCount)
              slices[i].write(`${path.dirname(this.filePath) + '/webtoons/' + path.basename(this.filePath, path.extname(this.filePath))}_${i}.jpg`)
              lastY += slices[i].bitmap.height
            }
          } else {
            image.write(`${path.dirname(this.filePath) + '/webtoons/' + path.extname(this.filePath, path.extname(this.filePath))}.jpg`)
          }
        })
        .catch(err => {
          if (err) {
            this.state = 3
            console.error(err)
          }
        })
      return true
    },
    async drop (e) {
      e.preventDefault()
      let _file = e.dataTransfer.files[0]

      if (!fs.statSync(_file.path).isFile()) {
        this.state = 3
      } else {
        this.state = 1
        this.filePath = _file.path

        let tapas = await this.generateTapas()
        let webtoons = await this.generateWebtoons()

        if (tapas && webtoons) {
          this.state = 2
        }
      }
    }
  },
  watch: {
    state () {
      if (this.state === 2) {
        shell.openExternal(path.dirname(this.filePath))
      }
    }
  }
}
</script>
