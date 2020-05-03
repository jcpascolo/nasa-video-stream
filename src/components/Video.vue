<template>
  <div class="video neumorphic">
    <video preload="auto">
      <source type='video/mp4' :src="getVideo()" />
    </video>
  </div>    
</template>

<script>
export default {
  props: ["videoSrc"],
  methods: {
    getVideo() {
      return require('../assets/'+this.videoSrc);
    }
  }
}
</script>

<style>
video {
  width: 100%;
  height: auto;
  border-radius: 25px;
}

.video {
  margin-top: 8px;
  margin-bottom: 8px;
  display:flex;
  justify-content: center;
  align-items: center;
  padding: 16px;
}
</style>
