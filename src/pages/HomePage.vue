<template>
  <h1 class="text-9xl font-nabla">GameDiff</h1>
  <p>
    Has it been awhile since you've played {{ randomGameName }}? Want to catch up fast without
    reading all the patch notes? We've got you covered.
  </p>
  <h4>We'll get started here, what's the game you want to play?</h4>
  <input type="text" />
</template>

<script setup>
import { onMounted } from 'vue'

const randomGameName = ['League of Legends', 'Deadlock', 'Counter-Strike 2']
//const gameName = ''

onMounted(() => {
  gameNameDisplay()
})

function gameNameDisplay() {
  print(randomGameName)
}
</script>
