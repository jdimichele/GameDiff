<template>
  <div
    class="relative min-h-screen bg-black text-white font-expletus overflow-x-hidden flex items-start justify-center px-6 pt-12 pb-16"
  >
    <main class="relative z-2 w-full max-w-170 flex flex-col gap-10">
      <header class="flex flex-col gap-5 animate-fade-down">
        <div class="flex flex-col gap-1"></div>
        <h1
          class="font-nabla text-[clamp(4rem,14vw,7.5rem)] leading-none text-[#00e5ff] [text-shadow:0_0_40px_rgba(0,229,255,0.35),0_0_80px_rgba(0,229,255,0.15)] tracking-tight"
        >
          GameDiff
        </h1>
        <div class="h-px w-full bg-linear-to-r from-[#00e5ff] to-transparent mt-2"></div>
        <p class="text-[1.05rem] font-light leading-relaxed flex flex-wrap items-center gap-1">
          Has it been awhile since you've played
          <span class="inline-block relative" aria-live="polite">
            <transition name="ticker" mode="out-in">
              <span
                :key="currentGame"
                class="inline-block font-expletus text-base text-[#4cff6d] bg-[rgba(76,255,100,0.08)] border border-[rgba(76,255,94,0.25)] px-[0.45em] py-[0.1em] rounded-sm"
                >{{ currentGame }}</span>
            </transition> ?
          </span>
          Want to catch up fast without reading all the patch notes? Look no further.
        </p>
      </header>

      <section class="relative bg-[#0d1117] border border-[#1e2d3d] rounded-sm p-8 flex flex-col gap-6 animate-fade-up">
        <div>
          <p class="font-expletus text-[0.7rem] text-[#4a7855] tracking-widest uppercase mb-3">
            <span class="text-[#00e5ff] mr-1">&gt;_</span> How should we find patch notes?
          </p>
          <div class="grid grid-cols-1 sm:grid-cols-3 gap-3">
            <button
            v-for="mode in MODES"
            :key="mode.id"
            class="flex flex-col gap-2 p-4 border rounded-sm text-left duration-200 cursor-pointer"
            :class="sourceMode === mode.id
                ? 'border-[#00e5ff] bg-[rgba(0,229,255,0.07)] shadow-[0_0_16px_rgba(0,229,255,0.12)]'
                : 'border-[#1e2d3d] hover:border-[#2e4d63] hover:bg-[rgba(0,229,255,0.03)]'"
              @click="sourceMode = mode.id"
              >
              <span class="text-xl">{{ mode.icon }}</span>
              <span class="font-expletus text-xs tracking-wide"
                  :class="sourceMode === mode.id ? 'text-[#00e5ff]' : 'text-[#c9d8e8]'">
                {{ mode.label }}
              </span>
              <span class="text-[0.68rem] text-[#4a6078] font-light leading-snug">{{ mode.desc }}</span>
            </button>
          </div>
        </div>


      </section>

      <footer></footer>
    </main>

  </div>
</template>

<script setup>
import { computed, onMounted, onBeforeUnmount, ref } from 'vue'

const MODES = [
  {
    id: 'search',
    icon: '🔍',
    label: 'Auto search',
    desc: 'We search for patch notes using the game name',
  },
  {
    id: 'manual',
    icon: '📋',
    label: 'Paste notes',
    desc: 'You paste the raw patch note text directly',
  },
]

const GAME_LIST = [
  'League of Legends',
  'Deadlock',
  'Counter-Strike 2',
  'Valorant',
  'Apex Legends',
  'Dota 2',
  'Fortnite',
  'Overwatch',
  'Teamfight Tactics',
  'Path of Exile 2',
]

const sourceMode = ref('search')

const needsGameName = computed(() => sourceMode.value === 'search')
const needsManual = computed(() => sourceMode.value === 'manual')

const currentGame = ref(GAME_LIST[0])
let tickerIndex = 0
let tickerTimer = null
function advanceTicker() {
  tickerIndex = (tickerIndex + 1) % GAME_LIST.length
  currentGame.value = GAME_LIST[tickerIndex]
}

const gameName = ref('')
const manualNotes = ref('')
const lastDatePlayed = ref('')
const lastVersionPlayed = ref('')
const showSuggestions = ref(false)


onMounted(() => {
  tickerTimer = setInterval(advanceTicker, 2400)
})
onBeforeUnmount(() => {
  clearInterval(tickerTimer)
})
</script>
