
<template>
  <div class="min-h-screen bg-gradient-to-br from-[#031d33] via-[#062b45] to-[#0a4f59] text-white flex">
    <div class="w-72 bg-[#041826]/90 backdrop-blur-md p-6 border-r border-cyan-900">
      <h1 class="text-4xl font-black mb-10 text-cyan-300">
        Ocean Beats
      </h1>

      <div class="space-y-4">
        <div
          v-for="(song, index) in songs"
          :key="index"
          @click="selectSong(index)"
          class="bg-[#0b2434] hover:bg-[#11374d] transition-all p-4 rounded-2xl cursor-pointer border border-cyan-900"
        >
          <div class="flex gap-4 items-center">
            <img :src="song.cover" class="w-20 h-20 rounded-xl object-cover" />

            <div>
              <h2 class="font-bold text-lg">
                {{ song.title }}
              </h2>

              <p class="text-cyan-200 text-sm">
                {{ song.artist }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="flex-1 flex items-center justify-center p-10">
      <div class="w-full max-w-2xl bg-[#071f2d]/90 border border-cyan-800 rounded-[2rem] p-8 shadow-2xl">
        <img
          :src="currentSong.cover"
          class="w-full h-[420px] object-cover rounded-3xl"
        />

        <div class="mt-8">
          <h1 class="text-4xl font-black">
            {{ currentSong.title }}
          </h1>

          <p class="text-cyan-200 text-xl mt-2">
            {{ currentSong.artist }}
          </p>

          <p class="text-cyan-100/80 mt-5 leading-relaxed">
            {{ currentSong.description }}
          </p>
        </div>

        <audio
          ref="audio"
          :src="currentSong.src"
          @timeupdate="updateProgress"
          @loadedmetadata="setDuration"
          @ended="playNextSong"
        ></audio>

        <div class="mt-8">
          <input
            type="range"
            min="0"
            :max="duration"
            v-model="currentTime"
            @input="seek"
            class="w-full accent-cyan-400"
          />

          <div class="flex justify-between text-sm text-cyan-200 mt-2">
            <span>{{ formatTime(currentTime) }}</span>
            <span>{{ formatTime(duration) }}</span>
          </div>
        </div>

        <div class="flex justify-center items-center gap-6 mt-10">
          <button
            @click="playPreviousSong"
            class="bg-cyan-700 hover:bg-cyan-600 transition px-5 py-3 rounded-full"
          >
            ⏮
          </button>

          <button
            @click="togglePlay"
            class="bg-emerald-400 hover:bg-emerald-300 transition text-black font-bold px-10 py-4 rounded-full text-xl"
          >
            {{ isPlaying ? 'Pause' : 'Play' }}
          </button>

          <button
            @click="playNextSong"
            class="bg-cyan-700 hover:bg-cyan-600 transition px-5 py-3 rounded-full"
          >
            ⏭
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'

const audio = ref(null)
const isPlaying = ref(false)
const currentTime = ref(0)
const duration = ref(0)
const currentSongIndex = ref(0)

const songs = ref([
  {
    title: 'SPONTAN ( tanpa ) UHUY!',
    artist: 'DEABDIL',
    src: '/music/spotan-tampa-u.mp3',
    cover: 'https://i.ytimg.com/vi/n0sxFjzZLIw/maxresdefault.jpg',
    description: 'A fun energetic meme-style track with chaotic internet vibes and upbeat rhythm.'
  },
  {
    title: 'Proof of a Hero - Fatalis',
    artist: 'Monster Hunter World: Iceborne',
    src: '/music/fatalis.mp3',
    cover: 'https://static.wikia.nocookie.net/monsterhunter/images/a/a2/MHWI-Fatalis_Render_001.png',
    description: 'The legendary final confrontation theme from Monster Hunter World: Iceborne. A powerful orchestral battle anthem.'
  }
])

const currentSong = computed(() => songs.value[currentSongIndex.value])

const selectSong = async (index) => {
  currentSongIndex.value = index
  await nextTick()
  playSong()
}

const playSong = async () => {
  if (!audio.value) return
  await audio.value.play()
  isPlaying.value = true
}

const togglePlay = async () => {
  if (!audio.value) return

  if (isPlaying.value) {
    audio.value.pause()
    isPlaying.value = false
  } else {
    await audio.value.play()
    isPlaying.value = true
  }
}

const playNextSong = async () => {
  currentSongIndex.value =
    (currentSongIndex.value + 1) % songs.value.length

  currentTime.value = 0

  await nextTick()
  playSong()
}

const playPreviousSong = async () => {
  currentSongIndex.value =
    (currentSongIndex.value - 1 + songs.value.length) % songs.value.length

  currentTime.value = 0

  await nextTick()
  playSong()
}

const updateProgress = () => {
  currentTime.value = audio.value.currentTime
}

const setDuration = () => {
  duration.value = audio.value.duration
}

const seek = () => {
  audio.value.currentTime = currentTime.value
}

const formatTime = (time) => {
  if (!time) return '0:00'

  const minutes = Math.floor(time / 60)
  const seconds = Math.floor(time % 60)
    .toString()
    .padStart(2, '0')

  return `${minutes}:${seconds}`
}
</script>
