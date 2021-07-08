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
        scale: 1,
        flip: true
      }
    }
  },
  methods: {
    applyTransforms() {
      let transforms = []
      transforms.push(`scale(${this.transforms.scale})`)
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
  margin: 0;
  padding: 0;
}

video {
  object-fit: cover;
  width: 400px;
  height: 100vh;
  background: #fff;
  -webkit-app-region: drag;
}

#app {
  mask-image: url("assets/blob.svg");
  mask-repeat: no-repeat;
  mask-size: 400px 400px;
  mask-position: center;
}
</style>
