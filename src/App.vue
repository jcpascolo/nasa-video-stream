<template>
  <div class="container">
    <div class="main-video">
      <div class="main">
        <video ref="mainVideo" :src="getCurrentVideo()" @timeupdate="updateSeekBarVideo()">
          <track 
            ref="subtitles"
            v-if="existSubt"
            label="English" 
            kind="subtitles" 
            srclang="en" 
            :src="getCurrentSubtitle()" 
            default
          >
        </video>
      </div>

      <div class="buttons">
        <div class="seek-bar">
          <button ref="stopPlay" class="neumorphic neumorphic-bottom" @click="playVideo()">Play</button>
          <div class="neumorphic neumorphic-bottom slider">
            <input type="range" ref="seekBar" value="0" @change="seekBarVideo()">
          </div>
        </div>
        <div class="sound">
          <button ref="mute" class="neumorphic neumorphic-bottom" @click="muteVideo()">Mute</button>
          <div class="neumorphic neumorphic-bottom slider">
            <input type="range" ref="volumeBar" min="0" max="1" step="0.1" value="1" @change="updateVolumeVideo()">
          </div>          
        </div>
        <div class="speed">
          <select class="neumorphic neumorphic-bottom" v-model="videoSpeed">
            <option disabled value="">Speed</option>
            <option value="0.5">0.5</option>
            <option value="1">1</option>
            <option value="1.5">1.5</option>
            <option value="2">2</option>
          </select>
        </div>        
        <div class="full-screen">
          <button class="neumorphic neumorphic-bottom" @click="fullScreenVideo()">Full Screen</button>
        </div>
        <div v-if="existSubt" class="subtitles">
          <button ref="subtButton" class="neumorphic neumorphic-bottom" @click="displaySubtitlesVideo()">Disable subtitles</button>
        </div>
        <div class="picture">
          <button class="neumorphic neumorphic-bottom" @click="takePicture()">Picture</button>
        </div>
      </div>
    </div>
    
    <div class="video-gallery">
      <h2>Video Gallery</h2>
      <div class="video-container" v-for="video in videos" :key="video.video" @click="changeCurrentVideo(video)">
        <Video  :videoSrc="video.video" />
      </div>      
    </div>

    <div ref="capturedImage" class="capturedImage"></div>

    <footer>
      <h3>Juan Carlos González Pascolo</h3>
    </footer>
  </div>
</template>

<script>
import Video from "./components/Video.vue";

export default {
  name: "App",
  components: {
    Video
  },
  data() {
    return {
      currentVideo: {
        video: "video1.mp4", 
        subtitles: "sub1.vtt"
      },
      existSubt: true,
      videoSpeed: 1,
      videos: [
        {
          video: "video1.mp4", 
          subtitles: "sub1.vtt"
        },
        {
          video: "video2.mp4", 
          subtitles: "sub2.vtt"
        },
        {
          video: "video3.mp4", 
          subtitles: "sub3.vtt"
        },
        {
          video: "video6.mp4", 
          subtitles: "sub6.vtt"
        },
        {
          video: "video4.mp4", 
          subtitles: "sub4.vtt"
        }
      ]
    };
  },
  methods: {
    getCurrentVideo() {
      return require('./assets/' + this.currentVideo.video);
    },
    getCurrentSubtitle() {
      try {
        console.log("ENTRA")
        console.log(this.currentVideo)
        return require('./assets/' + this.currentVideo.subtitles);
      }
      catch (err) {
        console.error(err);
        this.existSubt = false;
      }      
    },
    changeCurrentVideo(video) {
      this.existSubt = true;
      this.currentVideo = video;
      this.$refs.stopPlay.innerHTML = "Play";      
    },
    playVideo() {
      if (this.$refs.mainVideo.paused == true) {
        this.$refs.mainVideo.play();
        this.chageSpeed();
        this.$refs.stopPlay.innerHTML = "Pause";
      } else {
        this.$refs.mainVideo.pause();
        this.$refs.stopPlay.innerHTML = "Play";
      }
    },
    seekBarVideo() {
      this.$refs.mainVideo.currentTime = this.$refs.mainVideo.duration * (this.$refs.seekBar.value / 100);      
    },
    updateSeekBarVideo(){
      this.$refs.seekBar.value = (100 / this.$refs.mainVideo.duration) * this.$refs.mainVideo.currentTime;
    },    
    muteVideo() {
      if (this.$refs.mainVideo.muted == false) {
        this.$refs.mainVideo.muted = true;
        this.$refs.mute.innerHTML = "Unmute";
      } else {
        this.$refs.mainVideo.muted = false;
        this.$refs.mute.innerHTML = "Mute";
      }
    },
    updateVolumeVideo() {
      this.$refs.mainVideo.volume = this.$refs.volumeBar.value;
    },    
    chageSpeed() {
      this.$refs.mainVideo.playbackRate = parseFloat(this.videoSpeed);
    },
    fullScreenVideo() {
      if (this.$refs.mainVideo.requestFullscreen) {
        this.$refs.mainVideo.requestFullscreen();
      } 
      else if (this.$refs.mainVideo.mozRequestFullScreen) {
        this.$refs.mainVideo.mozRequestFullScreen(); // Firefox
      } else if (this.$refs.mainVideo.webkitRequestFullscreen) {
        this.$refs.mainVideo.webkitRequestFullscreen(); // Chrome and Safari
      }
    },
    displaySubtitlesVideo() {
      try{
        if(this.$refs.mainVideo.textTracks[0].mode == 'hidden') {
          this.$refs.mainVideo.textTracks[0].mode = 'showing';
          this.$refs.subtButton.innerHTML = "Disabled subtitles";
        }
        else {
          this.$refs.mainVideo.textTracks[0].mode = 'hidden';
          this.$refs.subtButton.innerHTML = "Enabled subtitles";
        }
      }
      catch {
        console.error("No subtitles found");
      }
    },
    takePicture() {
      let image= this.$refs.capturedImage;
      let video = this.$refs.mainVideo;
      let canvas = document.createElement("canvas");
      canvas.getContext('2d')
            .drawImage(video, 0, 0, canvas.width, canvas.height);

      let img = document.createElement("img");
      img.src = canvas.toDataURL();
      image.prepend(img);
    }
  },
  mounted(){
    this.chageSpeed();
  },
  watch: {
    videoSpeed: function() {
      this.chageSpeed();
    }
  }
};
</script>

<style>
* {
  box-sizing: border-box;
  outline: none;
}

html {
  background-color: #afd275;
}

img{
  margin: 8px;
}

footer {
  width: 100%;
  display: flex;
  justify-content: center;
  padding: 16px; 
}

.container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
}

.main-video {
  width: 70%;
}

.main {
  width: 100%;
  padding: 16px;
  background-color: rgba(0,0,0,1);
  border-radius: 8px;
}

.video-gallery {
  width: 22%;
  height: 94vh;
  padding: 16px;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  overflow: scroll;
  overflow-x: hidden;
  -ms-overflow-style: none;
  box-shadow: inset 6px 6px 10px 0 rgba(0, 0, 0, 0.2),
    inset -6px -6px 10px 0 rgba(255, 255, 255, 0.5);
  border-radius: 8px;  
}

.video-container {
  width: 96%;
}

.neumorphic {
  background-color: #afd275;
  box-shadow: 
    12px 12px 16px 0 rgba(0, 0, 0, 0.25),
    -8px -8px 12px 0 rgba(255, 255, 255, 0.3);
  border-radius: 25px;
}

.neumorphic:active {
  box-shadow: none;
  border: 1px solid rgba(0, 0, 0, 0.25);
}

.neumorphic-bottom {
  border: 0;
  width: 100px;
  height: 50px;
  text-align: center;
  appearance:none;
  cursor: pointer;
}

.buttons{
  padding: 10px;
  width: 100%;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
}

.seek-bar, .sound, .speed, .subtitles, .full-screen, .picture {
  margin-bottom: 16px;
}

.seek-bar, .sound, .slider {
  width: 100%;
  height: auto;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.slider {
  width: 90%;
  display: flex;
  justify-content: center;
  align-items: center;
}

input[type='range'] {
  width: 99%;
  height: auto;
  background-color: transparent;
  -webkit-appearance: none;
}

input[type=range]::-webkit-slider-thumb {
  -webkit-appearance: none;
}

input[type=range]:focus {
  outline: none; /* Removes the blue border. You should probably do some kind of focus styling for accessibility reasons though. */
}

input[type='range']::-webkit-slider-runnable-track {
  background-color: #000;
  border-radius: 20px;
  height: 5px;
}
    
input[type='range']::-webkit-slider-thumb {
  border: 1px solid rgb(230, 230, 230);
  background:rgb(240, 240, 240);
  height: 12px;
  width: 12px;
  border-radius: 50%;
  margin-top: -4px;
}

input[type="range"]::-moz-range-progress {
  background-color: rgba(206, 158, 158, 0.8); 
  border-radius: 20px;
  height: 5px;
}

input[type="range"]::-moz-range-track {  
  background-color: #000;
  border-radius: 20px;
  height: 5px;
}

.capturedImage {
  display:flex;
  justify-content: space-between;
  flex-wrap: wrap;
}

@media (max-width: 767.98px){
  body {
    margin-left: 16px;
    margin-right: 16px;
  }
  img {
    margin-right: 0;
    margin-left: 0;
  }

  select {
    text-indent: 15px;
  }

  .main-video {
    order:1;
    width: 100%;
  }

  .video-gallery {
    order:3;
    width: 100%;
  }

  .video {
    padding: 4px;
  }

  .capturedImage {
    order: 2;
    display:flex;
    justify-content: center;
    flex-wrap: wrap;
  }

  footer {
    order:4;
  }
}
</style>
