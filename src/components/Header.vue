<template>
  <div class="game__header">
    <h1 class="is-size-1">{{ name }}</h1>
    <div class="balance">
      <div class="balance__text">Balance: <span class="has-text-weight-bold">{{ displayBalance }}</span> credits</div>
      <button class="button is-primary"
              :disabled="balance > 0"
              @click="changeBalance"
      >Free Credits</button>
    </div>
  </div>
</template>

<script>
  import helpers from '../helpers'

  export default {
    name: 'GameHeader',
    props: {
      balance: {
        type: Number,
        required: true
      },
      name: String
    },
    computed: {
      displayBalance () {
        return helpers.round100(this.balance)
      }
    },
    methods: {
      changeBalance () {
        this.$emit('update:balance', this.balance + 100)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  .game__header {
    display: flex;
    justify-content: space-between;
    padding: 10px 20px ;
    align-items: center;
    margin-bottom: 30px;
  }

  .balance {
    .button {
      width: 100%;
    }
  }
</style>
