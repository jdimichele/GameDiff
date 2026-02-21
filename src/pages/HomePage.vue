<template>
  <h1>GameDiff</h1>
  <p>
    Has it been awhile since you've played {{ randomGameName }}? Want to catch up fast without
    reading all the patch notes? We've got you covered.
  </p>
  <h4>We'll get started here, what's the game you want to play?</h4>
  <input type="text" />
</template>

<script>
export default {
  data() {
    return {
      randomGameName: ['League of Legends', 'DotA2', 'Counter-Strike 2'],
      gameName: '',
    }
  },
  mounted() {
    this.gameNameDisplay()
  },
  methods: {},
}
</script>
