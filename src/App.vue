<template>
  <div id="app">
    <video id="video" ref="video" autoplay muted></video>
  </div>
</template>

<script>
import {ipcRenderer} from 'electron'

export default {
  name: 'App',
  data() {
    return {
      transforms: {
        x: 0,
        y: 0,
        scale: 1,
        flip: true
      }
    }
  },
  methods: {
    applyTransforms() {
      let transforms = []
      transforms.push(`scale(${this.transforms.scale})`)
      transforms.push(`translate(${this.transforms.x}%, ${this.transforms.y}%)`)
      if (this.transforms.flip) {
        transforms.push(`rotateY(180deg)`)
      }
      document.getElementById('video').style.transform = transforms.join(' ');
    }
  },
  mounted() {
    ipcRenderer.on('zoom', (event, zoom_type) => {
      switch (zoom_type) {
        case 'in':
          this.transforms.scale += 0.1
          break;
        case 'out':
          this.transforms.scale -= 0.1
          break;
        default:
          this.transforms.scale = 1;
          break;
      }
      this.applyTransforms()
    })
    ipcRenderer.on('move', (event, move_type) => {
      switch (move_type) {
        case 'up':
          this.transforms.y-=1;
          break
        case 'down':
          this.transforms.y+=1;
          break
        case 'left':
          this.transforms.x -= 1;
          break;
        case 'right':
          this.transforms.x += 1;
          break
      }
      this.applyTransforms()
    })
    ipcRenderer.on('flip', () => {
      this.transforms.flip = !this.transforms.flip
      this.applyTransforms()
    })
    window.addEventListener('keydown', event => {
      if (event.key === '/') {
        this.transforms.flip = !this.transforms.flip
        this.applyTransforms()
      }
    })
    navigator.mediaDevices.getUserMedia({
      video: true
    }).then((stream) => {
      this.applyTransforms()
      this.$refs.video.srcObject = stream
    })
  }
}
</script>

<style>
body {
  background: transparent;
  overflow: hidden;
  background: url("./assets/blob.svg");
  background-size: cover;
  background-position: center;
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}

video {
  object-fit: cover;
  width: 100%;
  height: auto;
  background: #fff;
  -webkit-app-region: drag;
}

#app {
  mask-image: url("assets/blob.svg");
  mask-repeat: no-repeat;
  mask-size: 100%;
  mask-position: center;
  width: 98%;
  height: 98vh;
  background: #ffffff;
}
</style>
