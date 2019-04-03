<template>
  <div class="wtube-container col-lg-12 col-md-12">
    <div class="search-player-container col-lg-9">
      <div class="search-container">
        <input class="col-lg-9 search" placeholder="Insert Youtube URL" ref="inputVideo">
        <button class="btn-play btn btn-danger" @click="addVideo()"><i class="fas fa-search"></i></button>
      </div>
      <div class="player-container col-lg-9">
        <div id="player"></div>
      </div>
    </div>
    <div class="video-container">
      <div v-for="info in infoList" :key="info.index" class="video-list" @click="playVid(info)">
        <div>{{info.play}}</div>
        <img :src="info.thumbnail">
        <div class="info-title col-lg-7" v-bind:class="{playing: playing}">{{info.title}}</div>
        <button class="btn btn-delete btn-outline-danger" @click="deleteVid(info.index)"><i class="fa fa-trash" aria-hidden="true"></i></button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Player',
  data() {
    return {
      newVideo: null,
      thisId: null,
      idList: [],
      ended: null,
      match: null,
      infoList: []
    }
  },
  methods: {
    addVideo() {
      this.newVideo = this.$refs.inputVideo.value.trim()
      this.youtubeParser(this.newVideo)
    },
    deleteVid(index) {
      this.infoList.splice(index, 1)
      this.idList.splice(index, 1)
    },
    playVid(info) {
      var YT = window.YT.get('player')
      YT.loadVideoById(info.id)
    },
    youtubeParser(url) {
      var YT = window.YT.get('player')
      var regExp = /^.*((youtu.be\/)|(v\/)|(\/u\/\w\/)|(embed\/)|(watch\?))\??v?=?([^#\&\?]*).*/
      this.match = url.match(regExp)
      if (this.match&&this.match[7].length === 11) {
        if (this.thisId === null) {
          this.addInfo()
          this.onYouTubeIframeAPIReady(this.thisId)
        } else {
          this.addInfo()
        }
      } 
    },
    addInfo() {
      this.thisId = this.match[7]
      var id = this.thisId
      this.idList.push(this.thisId)
      var thumbnail = (('http://img.youtube.com/vi/' + this.thisId + '/1.jpg').toString())
      var Url = 'https://www.googleapis.com/youtube/v3/videos?part=snippet&id=' +this.thisId + '&fields=items(id%2Csnippet)&key=AIzaSyDno0WtmfofiC9p2CeEAhhzjtVY36teiBE'
      fetch(Url)
        .then(data => {
          return data.json()
        })
        .then(res => {
          var title = res.items[0].snippet.title
          this.infoList.push({
            'index': this.infoList.length,
            'id': id,
            'thumbnail': thumbnail,
            'title': title,
            'play': ''
          })
        })
    },
    onYouTubeIframeAPIReady(idUrl) {
      var YT = window.YT
      var tmp = this
      var player = new YT.Player('player', {
        height: '720', 
        width: '1280',
        videoId: idUrl,
        playerVars: {
          'rel': 0,
          'enablejsapi': 1,
          'cc_load_policy': 0,
        },
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
        } else if (event.data === YT.PlayerState.ENDED) {
          window.idList = tmp.idList
          for(var i = 0; i < tmp.idList.length-1 ; i++) {
            if(idUrl === tmp.idList[i]) {
              idUrl = tmp.idList[i+1]
              player.loadVideoById(idUrl)
              return 
            }
          }
        }
      }
    }
  },
  props: {
    playing: Boolean
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
.wtube-container{
  display: flex;
  margin: 0 auto;
  padding: 50px 50px 0 50px;
}
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
.video-container {
  margin: 0 auto;
  font-weight: bold;
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 82vh;
  overflow: auto;
  padding-top: 26px;
}
.video-list {
  margin: 24px 0;
  display: flex;
  align-items: center;
  border-bottom: 2px solid #00000045;
  padding-bottom: 16px;
  width: 100%;
  cursor: pointer;
}
.info-title {
  text-align: left;
}
.playing {
  color: red;
}
</style>
