<template>
  <div id="hello">
    <div class="search-container">
      <input class="col-lg-3 search" placeholder="Insert Youtube URL" ref="inputVideo">
      <button class="btn-play btn btn-danger" @click="addVideo()"><i class="fas fa-search"></i></button>
    </div>
    <div id="player"></div>
    <div class="video-list"></div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      newVideo: null,
      thisId: null,
      videoList: []
    }
  },
  methods: {
    addVideo() {
      this.newVideo = this.$refs.inputVideo.value
      this.youtubeParser(this.newVideo)
    },
    youtubeParser(url) {
      var YT = window.YT.get('player')
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#\&\?]*).*/
      var match = url.match(regExp)
      if (match&&match[7].length === 11) {
        if (this.thisId === null) {
          this.thisId = match[7]
          this.onYouTubeIframeAPIReady(this.thisId)
        } else {
          this.thisId = match[7]
          this.videoList.push(this.thisId)
          console.log(this.videoList)
        }
      } 
    },
    onYouTubeIframeAPIReady(idUrl) {
      var YT
      var player
      YT = window.YT
      player = new YT.Player('player', {
        height: '390', 
        width: '640',
        videoId: idUrl,
        playerVars: { 'rel': 0 },
        events: {
          'onReady': onPlayerReady,
          'onStateChange': onPlayerStateChange
        }
      })
      function onPlayerReady(event) {
        event.target.playVideo()
      }
      function onPlayerStateChange(event) {
        if (event.data === YT.PlayerState.PLAYING) {
          player.getOptions()
          console.log('play')
        } else if (event.data === YT.PlayerState.ENDED) {
          console.log('ended')
          player.playVideo()
        } else if (event.data === YT.PlayerState.PAUSED) {
          console.log('paused')
        } else if (event.data === YT.PlayerState.BUFFERING) {
          console.log('buffering')
        } else {
          console.log('cued')
        }
      }
    }
  },
  mounted() {
      var tag = document.createElement('script');
      tag.src = "https://www.youtube.com/iframe_api"
      var firstScriptTag = document.getElementsByTagName('script')[0]
      firstScriptTag.parentNode.insertBefore(tag, firstScriptTag)
  }
}
</script>
<style scoped>
.search-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 16px;
}
.btn-play {
  border-radius: 0px;
  padding: 1px 20px;
  outline: 0!important;
  font-size: 20px;
  border-radius: 0px 10px 10px 0px;
}
.btn-danger:focus, .btn-danger:enabled, .btn-danger:active{
  box-shadow: none!important;
}
.search {    
  padding: 1px 10px;
  outline: none;
  font-size: 20px;
  border: 1px solid #00000038;
  border-radius: 10px 0px 0px 10px;
}
.search::placeholder {
  
}

</style>
