<template>
  <div id="app">
    <video ref="video" autoplay muted></video>
  </div>
</template>

<script>

export default {
  name: 'App',
  mounted() {
    navigator.mediaDevices.getUserMedia({
      video: true
    }).then((stream)=>{
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
  /*-webkit-app-region: drag;*/
}

video {
  mask-image: url("assets/blob.svg");
  mask-repeat: no-repeat;
  mask-size: 400px 400px;
  mask-position: center;
}
</style>
