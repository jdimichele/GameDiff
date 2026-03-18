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
                >{{ currentGame }}</span
              >
            </transition>
            ?
          </span>
          Want to catch up fast without reading all the patch notes? Look no further.
        </p>
      </header>

      <section
        class="relative bg-[#0d1117] border border-[#1e2d3d] rounded-sm p-8 flex flex-col gap-6 animate-fade-up"
      >
        <div>
          <p class="font-expletus text-[0.7rem] text-[#4a7855] tracking-widest uppercase mb-3">
            <span class="text-[#00e5ff] mr-1">&gt;_</span> How should we find patch notes?
          </p>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-2">
            <button
              v-for="mode in MODES"
              :key="mode.id"
              class="flex flex-col gap-2 p-4 border rounded-sm text-left duration-200 cursor-pointer"
              :class="
                sourceMode === mode.id
                  ? 'border-[#00e5ff] bg-[rgba(0,229,255,0.07)] shadow-[0_0_16px_rgba(0,229,255,0.12)]'
                  : 'border-[#1e2d3d] hover:border-[#2e4d63] hover:bg-[rgba(0,229,255,0.03)]'
              "
              @click="sourceMode = mode.id"
            >
              <span class="text-xl">{{ mode.icon }}</span>
              <span
                class="font-expletus text-xs tracking-wide"
                :class="sourceMode === mode.id ? 'text-[#00ff6a]' : 'text-[#c9d8e8]'"
              >
                {{ mode.label }}
              </span>
              <span class="text-[0.68rem] text-[#4a7850] font-light leading-snug">{{
                mode.desc
              }}</span>
            </button>
          </div>
        </div>

        <transition name="section-fade">
          <div v-if="needsGameName">
            <label
              class="block font-expletus text-[0.7rem] text-[#4a7850] tracking-[0.1rem] uppercase mb-1.5"
              for="game-input"
            >
              <span class="text-[#00e5ff] mr-1">&gt;_</span> Game name
            </label>
            <div class="flex gap-3 sm:flex-row flex-col">
              <input
                type="text"
                id="game-input"
                v-model="gameName"
                class="flex-1 bg-[rgba(0,229,255,0.03)] border border-[#1e2d3d] rounded-sm text-[#c9d8e8] font-expletus text-sm px-3.5 py-2.5 outline-none transition-all duration-200 placeholder-[#4a6078] focus:border-[#00e5ff] focus:shadow-[0_0_0_2px_rgba(0,229,255,0.08)]"
                placeholder="e.g. League of Legends, Deadlock..."
                autocomplete="off"
                spellcheck="false"
                @input="onInput"
              />
            </div>
            <transition name="fade">
              <ul
                v-if="suggestions.length > 0 && showSuggestions"
                class="list-none bg-[#0f2313] border border-[#1e3d27] rounded-sm overflow-hidden mt-1"
              >
                <li
                  v-for="s in suggestions"
                  :key="s"
                  class="font-expletus text-sm text-white px-3.5 py-2 cursor-pointer flex items-center gap-2.5 border-b border-[rgba(30,61,40,0.5)] last:border-b-0 transition-all duration-150 hover:bg-[rgba(0,255,60,0.07)] hover:text-[#00ff5e]"
                  @mousedown.prevent="selectSuggestion(s)"
                >
                  <span class="text-[#4a7850] text-[0.7rem] opacity-60">◈</span> {{ s }}
                </li>
              </ul>
            </transition>
          </div>
        </transition>

        <transition name="section-fade">
          <div v-if="needsManual">
            <div v-if="needsGameName" class="flex items-center gap-4 mb-5">
              <span class="flex-1 h-px bg-[#203d1e]"></span>
              <span
                class="text-expletus text-[0.68rem] text-[#4a7850] tracking-[0.08em] uppercase whitespace-nowrap"
                >+ paste notes directly</span
              >
              <span class="flex-1 h-px bg-[#203d1e]"></span>
            </div>
            <label
              class="block font-expletus text-[0.7rem] text-[#4a7850] tracking-widest uppercase mb-1.5"
              for="manual-input"
            >
              <span class="text-[#00e5ff] mr-1">&gt;_</span> Paste patch notes
            </label>
            <textarea
              id="manul-input"
              v-model="manualNotes"
              class="w-full bg-[rgba(0,229,255,0.03)] border border-[#1e2d3d] rounded-sm text-[#c9d8e8] font-['Share_Tech_Mono',monospace] text-sm px-3.5 py-2.5 outline-none transition-all duration-200 placeholder-[#4a6078] focus:border-[#00e5ff] focus:shadow-[0_0_0_2px_rgba(0,229,255,0.08)] resize-y min-h-22.5"
              placeholder="Paste patch notes here."
              rows="4"
            ></textarea>
          </div>
        </transition>

        <div>
          <p class="font-expletus text-[0.7rem] text-[#4a7850] tracking-widest uppercase mb-3">
            <span class="text-[#00e5ff] mr-1">&gt;_</span> When did you last play?
          </p>
          <div class="flex sm:flex-row flex-col gap-4 items-end">
            <div class="flex-1">
              <label
                class="block font-expletus text-[0.65rem] text-[#4a7850] tracking-[0.08em] uppercase mb-1.5"
                for="lp-date"
                >Date</label
              >
              <input
                id="lp-date"
                type="date"
                v-model="lastDatePlayed"
                class="w-full bg-[rgba(0,229,255,0.03)] border border-[#1e2d3d] rounded-sm text-[#c9d8e8] font-expletus text-sm px-3.5 py-2.5 outline-none transition-all duration-200 focus:border-[#00ff6a] focus:shadow-[0_0_0_2px_rgba(0,229,255,0.08)] scheme-dark"
              />
            </div>
            <div class="font-expletus text-xs text-[#4a7850] pb-3 shrink-0 sm:block hidden">or</div>
            <div class="flex-1">
              <label
                class="block font-expletus text-[0.65rem] text-[#4a7850] tracking-[0.08em] uppercase mb-1.5"
                for="lp-date"
                >Version</label
              >
              <input
                id="lp-version"
                type="text"
                v-model="lastVersionPlayed"
                class="w-full bg-[rgba(0,229,255,0.03)] border border-[#1e2d3d] rounded-sm text-[#c9d8e8] font-expletus text-sm px-3.5 py-2.5 outline-none transition-all duration-200 focus:border-[#00ff6a] focus:shadow-[0_0_0_2px_rgba(0,229,255,0.08)] scheme-dark"
                plac
              />
            </div>
          </div>
        </div>

        <button
          class="mt-1 border rounded-sm font-expletus text-sm px-6 py-3.5 text-center tracking-[0.05em] transition-all duration-200"
          :class="
            canSubmit
              ? 'border-[#00ff55] bg-[#00ff5e] text-[#08100c] cursor-pointer shadow-[0_0_24px_rgba(0,229,255,0.3)] hover:shadow-[0_0_40px_rgba(0,229,255,0.45)] hover:-translate-y-px'
              : 'border-[#1e3d2b] text-[#4a785a] cursor-not-allowed'
          "
          :disabled="!canSubmit"
          @click="handleSubmit"
        >
          <span v-if="!canSubmit" class="text-xs"> {{ ctaHint }}</span>
          <span
            v-else
            class="flex items-center justify-center gap-3 font-medium tracking-widest uppercase"
          >
            <span class="relative w-2 h-2 rounded-full bg-black shrink-0">
              <span
                class="absolute -inset-0.75 rounded-full border border-black animate-ping opacity-75"
              ></span>
            </span>
            Generate patch diff
          </span>
        </button>
      </section>

      <footer class="font-expletus text-[0.68rem] text-[#4a7854] text-center flex items-center justify-center gap-2.5 animate-fade-up [animation-delay:0.3s]">
        <span class="bg-[rgba(0,229,255,0.08)] border border-[rgba(0,229,255,0.15)] text-[#00ff37] px-1.5 py-0.5 rounded-sm text-[0.65rem]">v0.1.0</span>
        <span class="opacity-30">·</span>
        <span>No more reading 47 patch notes just to start gaming again.</span>
      </footer>
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

const currentGame = ref(GAME_LIST[0])
let tickerIndex = 0
let tickerTimer = null
function advanceTicker() {
  tickerIndex = (tickerIndex + 1) % GAME_LIST.length
  currentGame.value = GAME_LIST[tickerIndex]
}

const sourceMode = ref('search')

const needsGameName = computed(() => sourceMode.value === 'search')
const needsManual = computed(() => sourceMode.value === 'manual')

const gameName = ref('')
const manualNotes = ref('')
const lastDatePlayed = ref('')
const lastVersionPlayed = ref('')
const showSuggestions = ref(false)

const suggestions = computed(() => {
  const q = gameName.value.trim().toLowerCase()
  if (!q) return []
  return GAME_LIST.filter((g) => g.toLowerCase().includes(q)).slice(0, 5)
})

const canSubmit = computed(() => {
  const hasGame = !needsGameName.value || gameName.value.trim().length > 0
  const hasManual = !needsManual.value || manualNotes.value.trim().length > 0
  const hasLastPlayed = lastDatePlayed.value || lastVersionPlayed.value.trim().length > 0
  const hasSource = sourceMode.value
    ? gameName.value.trim().length > 0 || manualNotes.value.trim().length > 0
    : hasGame && hasManual
  return hasSource && hasLastPlayed
})

const ctaHint = computed(() => {
  const hasLastPlayed = lastDatePlayed.value || lastVersionPlayed.value.trim().length > 0
  if (!hasLastPlayed) return 'Add the last played date (roughly) or version to continue.'
  if (needsGameName.value && !gameName.value.trim())
    return 'Enter the game you want to compare to continue.'
  if (needsManual.value && !manualNotes.value.trim())
    return 'Paste the patch notes you want to compare to continue.'
  return 'Fill in above fields in order to continue.'
})

const emit = defineEmits(['submit'])

function handleSubmit() {
  if (!canSubmit.value) {
    return
  }
  const payload = {
    sourceMode: sourceMode.value,
    gameName: gameName.value,
    manualNotes: manualNotes.value,
    lastDatePlayed: lastDatePlayed.value,
    lastVersionPlayed: lastVersionPlayed.value,
  }
  console.log('Diff submitted', payload)
  emit('submit', payload)
  router.push({ name: 'patchdiff', state: payload })
}

function onInput() {
  showSuggestions.value = true
}

function selectSuggestion(s) {
  gameName.value = s
  showSuggestions.value = false
}

onMounted(() => {
  tickerTimer = setInterval(advanceTicker, 2400)
})
onBeforeUnmount(() => {
  clearInterval(tickerTimer)
})
</script>

<style scoped>
@keyframes fade-down {
  from {
    opacity: 0;
    transform: translateY(-16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
@keyframes fade-up {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.animate-fade-down {
  animation: fade-down 0.6s ease;
}

.animate-fade-up {
  animation: fade-up 0.6s ease;
}

.ticker-enter-active {
  animation: fade-up 0.5s ease;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
.section-fade-enter-active,
.section-fade-leave-active {
  transition:
    opacity 0.25s,
    transform 0.25s;
}
.section-fade-enter-from,
.section-fade-leave-to {
  opacity: 0;
  transform: translateY(-6px);
}
</style>
