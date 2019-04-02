<template>
  <div id="hello">
    <div class="search">
      <input ref="inputVideo">
      <button class="btn-primary btn btn-play" @click="addVideo()">Play</button>
    </div>
    <div id="player"></div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      newVideo: null,
      thisId: null
    }
  },
  methods: {
    addVideo() {
      this.newVideo = this.$refs.inputVideo.value
      this.youtubeParser(this.newVideo)
    },
    youtubeParser(url) {
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#\&\?]*).*/
      var match = url.match(regExp)
      if (match&&match[7].length === 11) {
        if (this.thisId === null) {
          this.thisId = match[7]
          this.onYouTubeIframeAPIReady(this.thisId)
        } else {
          this.thisId = match[7]
          this.onYouTubeIframeAPIReady(this.thisId)
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
          'onReady': onPlayerReady
        }
      })
      function onPlayerReady(event) {
        event.target.playVideo()
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
.btn-play {
  margin-left: 20px;
}
</style>
