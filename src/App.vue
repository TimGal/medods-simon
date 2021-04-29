<template>
  <div id="app">
    <SimonGame msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import SimonGame from './components/SimonGame.vue'

export default {
  name: 'App',
  components: {
    SimonGame
  }
}
</script>

<style lang="sass">
@import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400&display=swap')

*
  margin: 0
  padding: 0
  box-sizing: border-box
  transition: .5s easy-in-out
</style>
