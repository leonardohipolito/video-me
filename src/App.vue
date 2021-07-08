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
        scale: 1
      }
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
      document.getElementById('video').style.transform = `scale(${this.transforms.scale})`
    })
    navigator.mediaDevices.getUserMedia({
      video: true
    }).then((stream) => {
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
