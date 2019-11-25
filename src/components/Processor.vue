<template>
  <div class="flex flex-col justify-around items-center m-auto" style="width: calc(100% - 32px); height: calc(100% - 32px);">
    <div @dragover.prevent @drop="drop($event)" class="flex flex-col justify-center items-stretch py-10 w-full h-full" :class="{'border-4 border-gray-300 border-dashed hover:border-solid rounded':(state === 0) || (state === 2) || (state === 3)}">
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
    async generateStrip (maxWidth, maxHeight, extName, outDir) {
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
              slices[i].write(`${path.dirname(this.filePath)}/${outDir}/${path.basename(this.filePath, path.extname(this.filePath))}_${i}${extName}`)
              lastY += slices[i].bitmap.height
            }
          } else {
            image.write(`${path.dirname(this.filePath)}/${outDir}/${path.basename(this.filePath, path.extname(this.filePath))}${extName}`)
          }
        })
        .catch(err => {
          if (err) {
            this.state = 3
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

        let isPNG = path.extname(this.filePath) === '.png'
        let isJPEG = (path.extname(this.filePath) === '.jpg') || (path.extname(this.filePath) === '.jpeg')

        if (isPNG || isJPEG) {
          let tapas = await this.generateStrip(940, 4000, '.png', 'tapas')
          let webtoons = await this.generateStrip(800, 1280, '.jpg', 'webtoons')

          if (tapas && webtoons) {
            this.state = 2
          }
        } else {
          this.state = 3
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
