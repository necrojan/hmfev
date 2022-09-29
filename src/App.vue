<template>
  <div id="app">
    <GameManager/>
  </div>
</template>

<script>
import GameManager from './components/GameManager'
import ChallengeAttempt from './components/ChallengeAttempt'

export default {
  name: 'App',
  components: {
    GameManager,
    ChallengeAttempt
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
