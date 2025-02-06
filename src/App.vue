<template>
  <div id="app">
    <GameRules />
    <NeverHaveIEver />
  </div>
</template>

<script>
import NeverHaveIEver from './components/NeverHaveIEver.vue'
import GameRules from './components/GameRules.vue'

export default {
  name: 'App',
  components: {
    NeverHaveIEver,
    GameRules
  }
}
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-top: 60px;
}
</style> 