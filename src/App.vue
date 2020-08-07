<template>
  <div id="app">
    <header>
      <h1>Wills Music</h1>
    </header>
    <main>
      <section class="player">
        <h2 class="song-title">{{ current.title }} - <span>{{ current.artist }}</span></h2>
        <div class="controls">
          <button class="prev" @click="prev">Prev</button>
          <button class="play" v-if="!isPlaying" @click="play">Play</button>
          <button class="pause" v-else @click="pause">Pause</button>
          <button class="next" @click="next">Next</button>
        </div>
        <div class="seek">
          <div class="current" :class="typeof currentTime === 'number' ? 'visible' : 'hidden'">Current Time: {{ Math.floor(currentTime / 60) }}:{{ Math.floor(currentTime % 60) }}</div>
          <input :class="isPlaying ? 'visible' : 'hidden'" type="range" id="seek" :value="currentTime" :max="this.duration" @mousedown="pause" @mouseup="seek" />
          <div class="length" :class="typeof this.duration === 'number' ? 'visible' : 'hidden'">Song duration: {{ Math.round(this.duration / 60) }}:{{ Math.round(this.duration % 60) }}</div>
        </div>
      </section>
      <section class="playlist">
        <h3>The Playlist</h3>
        <button v-for="song in songs" :key="song" @click="play(song)" :class="(song.src === current.src) ? 'song playing' : 'song'">
          {{ song.title }} - {{ song.artist }}
        </button>
      </section>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      current: {},
      index: 0,
      isPlaying: false,
      player: new Audio(),
      duration: Number,
      currentTime: String,
      songs:[
        {
          title: 'Hurricane',
          artist: 'Bob Dylan',
          src: require('@/assets/Bob-Dylan-Hurricane.mp3')
        },
        {
          title: 'Need You Tonight',
          artist: 'INXS',
          src: require('@/assets/INXS-Need-You-Tonight.mp3')
        }
      ],
    }
  },
  methods: {
    play(song) {
      if (typeof song.src !== 'undefined') {
        this.current = song
        this.player.src = this.current.src
      }
      this.player.play()
      this.player.addEventListener('ended', function() {
        this.index++
        if (this.index > this.songs.length - 1) {
          this.index = 0
        }
        this.current = this.songs[this.index]
        this.play(this.current)
      }.bind(this))
      this.isPlaying = true
    },
    pause() {
      this.player.pause()
      this.isPlaying = false
    },
    prev() {
      this.index--
      if (this.index < 0) {
        this.index = this.songs.length - 1
      }
      this.current = this.songs[this.index]
      this.play(this.current)
    },
    next() {
      this.index++
      if (this.index > this.songs.length - 1) {
        this.index = 0
      }
      this.current = this.songs[this.index]
      this.play(this.current)
    },
    updateTime() {
      this.currentTime = this.player.currentTime
    },
    seek(e) {
      this.currentTime = e.target.value
      this.play(this.current)
      this.player.currentTime = Number(this.currentTime)
    }
  },
  created() {
    this.current = this.songs[this.index]
    this.player.src = this.current.src
    this.player.addEventListener('playing', function () {
      this.duration = this.player.duration
      setInterval(this.updateTime, 1000)
    }.bind(this))
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: sans-serif;
}

header {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px;
  background-image: linear-gradient(to left, lightcoral, darkcyan);
  color: white;
}

main {
  width: 100%;
  max-width: 768px;
  margin: 0 auto;
  padding: 25px;
}

.song-title {
  color: #53565a;
  font-size: 32px;
  font-weight: 700;
  text-transform: uppercase;
  text-align: center;
}

.song-title span {
  font-weight: 400;
  font-style: italic;
}

.controls {
  display: flex;
  justify-content: center;
  padding: 30px 15px;
}

button {
  appearance: none;
  background: none;
  border: none;
  outline: none;
  cursor: pointer;
  transition: 0.4s;
}

button:hover {
  transform: scale(1.1);
}

.play {
  font-size: 20px;
  font-weight: 700;
  padding: 15px 25px;
  margin: 0 15px;
  border-radius: 8px;
  color: white;
  background: mediumaquamarine;
}

.pause {
  font-size: 20px;
  font-weight: 700;
  padding: 15px 25px;
  margin: 0 15px;
  border-radius: 8px;
  color: white;
  background: maroon;
}

.play:active, .pause:active {
  transform: scale(0.95);
}

.play:hover {
  background: aquamarine;
}

.pause:hover {
  background: lightcoral;
}

.next, .prev {
  font-size: 16px;
  font-weight: 700;
  padding: 10px 20px;
  margin: 0 15px;
  border-radius: 6px;
  color: white;
  background: #ff5858;
  transition: 0.2s;
}

.next:hover, .prev:hover {
  transform: scale(0.95);
}

.playlist {
  padding: 40px 30px;
}

.playlist h3 {
  color: #212121;
  font-size: 28px;
  font-weight: 400;
  margin-bottom: 30px;
  text-align: center;
}

.playlist .song {
  display: block;
  width: 100%;
  padding: 15px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
}

.playlist .song:hover {
  color: lightcoral;
}

.playlist h3 {
  font-weight: 700;
}

.playlist .song.playing {
  color: white;
  background-image: linear-gradient(to right, red, lightcoral);
}

.seek {
  margin: 0;
  padding: 10px;
  display: block;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.seek div {
  font-size: 20px;
  font-weight: bold;

}

.seek .hidden {
  color: transparent;
}

input[type=range] {
  height: 38px;
  -webkit-appearance: none;
  margin: 10px 0;
  width: 100%;
}
input[type=range]:focus {
  outline: none;
}
input[type=range]::-webkit-slider-runnable-track {
  width: 100%;
  height: 13px;
  cursor: pointer;
  animate: 0.2s;
  box-shadow: 1px 1px 1px #000000;
  background-image: linear-gradient(to right, lightpink, lightcoral);
  border-radius: 0px;
  border: 1px solid #000000;
}
input[type=range]::-webkit-slider-thumb {
  box-shadow: 1px 1px 1px #000000;
  border: 1px solid #000000;
  height: 30px;
  width: 15px;
  border-radius: 5px;
  background: #FFFFFF;
  cursor: pointer;
  -webkit-appearance: none;
  margin-top: -9.5px;
}
input[type=range]:focus::-webkit-slider-runnable-track {
  background: #3071A9;
}
input[type=range]::-moz-range-track {
  width: 100%;
  height: 13px;
  cursor: pointer;
  animate: 0.2s;
  box-shadow: 1px 1px 1px #000000;
  background: #3071A9;
  border-radius: 0px;
  border: 1px solid #000000;
}
input[type=range]::-moz-range-thumb {
  box-shadow: 1px 1px 1px #000000;
  border: 1px solid #000000;
  height: 30px;
  width: 15px;
  border-radius: 5px;
  background: #FFFFFF;
  cursor: pointer;
}
input[type=range]::-ms-track {
  width: 100%;
  height: 13px;
  cursor: pointer;
  animate: 0.2s;
  background: transparent;
  border-color: transparent;
  color: transparent;
}
input[type=range]::-ms-fill-lower {
  background: #3071A9;
  border: 1px solid #000000;
  border-radius: 0px;
  box-shadow: 1px 1px 1px #000000;
}
input[type=range]::-ms-fill-upper {
  background: #3071A9;
  border: 1px solid #000000;
  border-radius: 0px;
  box-shadow: 1px 1px 1px #000000;
}
input[type=range]::-ms-thumb {
  margin-top: 1px;
  box-shadow: 1px 1px 1px #000000;
  border: 1px solid #000000;
  height: 30px;
  width: 15px;
  border-radius: 5px;
  background: #FFFFFF;
  cursor: pointer;
}
input[type=range]:focus::-ms-fill-lower {
  background: #3071A9;
}
input[type=range]:focus::-ms-fill-upper {
  background: #3071A9;
}
</style>
