<template>
  <div id="app" class="game">
    <Header :name="name"
        :balance.sync="balance"
    />
    <Dice :balance.sync="balance"/>
  </div>
</template>

<script>
import Dice from './components/Dice.vue'
import Header from './components/Header.vue'

export default {
  name: 'GameFrame',
  components: {
    Dice,
    Header
  },
  data() {
    return {
      balance: 18,
      name: 'Endpass Dice'
    }
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
