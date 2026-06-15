<template>
  <div class="min-h-screen bg-black text-white flex">
    <div class="w-64 bg-[#121212] p-5 hidden md:block">
      <h1 class="text-3xl font-bold mb-8 text-green-500">Vue Music</h1>

      <div class="space-y-2">
        <button @click="activePage = 'home'" class="w-full text-left p-3 rounded hover:bg-[#282828]">
          🏠 Home
        </button>

        <button @click="activePage = 'player'" class="w-full text-left p-3 rounded hover:bg-[#282828]">
          🎵 Music Player
        </button>

        <button @click="activePage = 'details'" class="w-full text-left p-3 rounded hover:bg-[#282828]">
          📀 Song Details
        </button>

        <button @click="activePage = 'profile'" class="w-full text-left p-3 rounded hover:bg-[#282828]">
          👤 Profile
        </button>
      </div>
    </div>

    <div class="flex-1 p-8">

      <div v-if="activePage === 'home'">
        <h1 class="text-4xl font-bold mb-8">Welcome Back 👋</h1>

        <div class="bg-[#181818] p-6 rounded-3xl max-w-md cursor-pointer" @click="activePage = 'player'">
          <img :src="currentSong.cover" class="rounded-2xl w-full h-64 object-cover" />

          <h2 class="text-2xl font-bold mt-4">
            {{ currentSong.title }}
          </h2>

          <p class="text-gray-400">
            {{ currentSong.artist }}
          </p>
        </div>
      </div>

      <div v-if="activePage === 'player'" class="max-w-md mx-auto bg-[#181818] p-8 rounded-3xl">
        <img :src="currentSong.cover" class="w-full h-80 object-cover rounded-2xl" />

        <div class="text-center mt-6">
          <h2 class="text-2xl font-bold">{{ currentSong.title }}</h2>
          <p class="text-gray-400">{{ currentSong.artist }}</p>
        </div>

        <audio
          ref="audio"
          :src="currentSong.src"
          @timeupdate="updateProgress"
          @loadedmetadata="setDuration"
          @ended="nextSong"
        ></audio>

        <input
          type="range"
          min="0"
          :max="duration"
          v-model="currentTime"
          @input="seek"
          class="w-full mt-6 accent-green-500"
        />

        <div class="flex justify-center gap-4 mt-8 flex-wrap">
          <button @click="previousSong" class="bg-gray-700 px-5 py-3 rounded-full hover:bg-gray-600">
            ⏮ Prev
          </button>

          <button @click="togglePlay" class="bg-green-500 text-black px-6 py-3 rounded-full font-bold">
            {{ isPlaying ? 'Pause' : 'Play' }}
          </button>

          <button @click="nextSong" class="bg-gray-700 px-5 py-3 rounded-full hover:bg-gray-600">
            Next ⏭
          </button>
        </div>
      </div>

      <div v-if="activePage === 'details'">
        <h1 class="text-4xl font-bold mb-6">Song Details</h1>

        <div class="bg-[#181818] p-8 rounded-3xl max-w-3xl">
          <img :src="currentSong.cover" class="w-full h-96 object-cover rounded-2xl mb-6" />

          <h2 class="text-5xl font-bold">
            {{ currentSong.title }}
          </h2>

          <p class="text-2xl text-gray-400 mt-4">
            {{ currentSong.artist }}
          </p>

          <p class="mt-6 text-lg">
            Featured demo song for the Vue Spotify Music Player.
          </p>
        </div>
      </div>

      <div v-if="activePage === 'profile'">
        <h1 class="text-4xl font-bold mb-6">User Profile</h1>

        <div class="bg-[#181818] p-8 rounded-3xl max-w-2xl">
          <img src="https://i.pravatar.cc/200" class="w-32 h-32 rounded-full border-4 border-green-500 mb-6" />

          <h2 class="text-3xl font-bold">Music Lover</h2>

          <p class="text-gray-400 mt-2">
            Premium Listener
          </p>
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
const activePage = ref('home')
const currentSongIndex = ref(0)

const songs = ref([
  {
    title: 'SPONTAN ( tanpa ) UHUY!',
    artist: 'DEABDIL',
    src: '/music/spotan-tampa-u.mp3',
    cover: 'https://i.ytimg.com/vi/n0sxFjzZLIw/maxresdefault.jpg'
  },
  {
    title: 'Proof of a Hero - Fatalis BGM 3',
    artist: 'Monster Hunter World: Iceborne',
    src: '/music/fatalis-bgm3.mp3',
    cover: 'https://i.ytimg.com/vi/qMSg1A6I8QI/maxresdefault.jpg'
  }
])

const currentSong = computed(() => songs.value[currentSongIndex.value])

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

const playCurrentSong = async () => {
  await nextTick()

  if (!audio.value) return

  currentTime.value = 0

  try {
    await audio.value.play()
    isPlaying.value = true
  } catch (error) {
    isPlaying.value = false
  }
}

const nextSong = async () => {
  currentSongIndex.value = (currentSongIndex.value + 1) % songs.value.length
  await playCurrentSong()
}

const previousSong = async () => {
  currentSongIndex.value =
    (currentSongIndex.value - 1 + songs.value.length) % songs.value.length

  await playCurrentSong()
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
</script>
