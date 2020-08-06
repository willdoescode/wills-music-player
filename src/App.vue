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
      player: new Audio(),
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
    }
  },
  created() {
    this.current = this.songs[this.index]
    this.player.src = this.current.src
  },
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
  padding: 0 30px;
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
</style>
