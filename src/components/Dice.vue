<template>
  <div class="dice">
    <div class="dice__pad">
      <div class="input__group">
        <label class="label" for="bet_amount">Bet Amount</label>
        <input type="number"
               class="input"
               id="bet_amount"
               :value="betAmount"
               @input="checkAmount"
        />
      </div>
      <div class="input__group">
        <label class="label" for="bet_number">Number</label>
        <input type="number"
               class="input"
               id="bet_number"
               :value="betNumber"
               @input="checkNumber"
        />
      </div>
      <div class="buttons">
        <div class="button__group">
          <button class="button button_bet is-success"
            :disabled="isDisabled"
            @click="getResult(true)"
          >Bet Hi</button>
          <div class="description">
            <div class="bet__chance is-size-7">Chance: <span class="has-text-weight-bold ">{{ 100 - betNumber }}%</span></div>
            <div class="bet__payout is-size-7">Payout: <span class="has-text-weight-bold">{{ payoutHi }}x</span></div>
          </div>
        </div>
        <div class="button__group">
          <button class="button button_bet is-success"
            :disabled="isDisabled"
                  @click="getResult(false)"
          >Bet Low</button>
          <div class="description">
            <div class="bet__chance is-size-7">Chance: <span class="has-text-weight-bold ">{{ betNumber }}%</span></div>
            <div class="bet__payout is-size-7">Payout: <span class=" has-text-weight-bold ">{{ payoutLow }}x</span></div>
          </div>
        </div>
      </div>
    </div>
    <div class="dice__info">
      <div class="result">
        <div class="has-text-weight-bold">Result</div>
        <div class="has-text-weight-bold is-size-2">{{ message }}</div>
      </div>
      <div class="hash">
        <div class="hash__title">Provably Fair Hash</div>
        <div class="hash__value has-text-weight-bold">{{ hashValue }}</div>
      </div>
    </div>
  </div>
</template>

<script>
import md5 from 'js-md5'
import helpers from '../helpers'


export default {
  name: 'Dice',
  props: {
    balance: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      message: '',
      randomValue: this.getRandomValue(),
      betAmount: 0,
      betNumber: 50,
      isButtonBlocked: false
    }
  },
  computed: {
    hashValue () {
      return md5(this.randomValue+'')
    },
    payoutHi () {
      return helpers.round100(100 / (100 - this.betNumber))
    },
    payoutLow () {
      return helpers.round100(100 / this.betNumber)
    },
    isDisabled () {
      return this.balance < 0 || this.betAmount > this.balance || this.isButtonBlocked
    }
   },
  methods: {
    getRandomValue () {
      return Math.floor(Math.random()*100 + 1)
    },
    checkAmount (event) {
      event.target.classList.remove('is-danger')
      if (event.target.value > 0 && event.target.value <= this.balance) {
        this.betAmount = +event.target.value
        this.isButtonBlocked = false
      } else {
        event.target.classList.add('is-danger')
        this.isButtonBlocked = true
      }
    },
    checkNumber (event) {
      event.target.classList.remove('is-danger')
      const number = +event.target.value
      if (number > 0 && number < 100 && number === parseInt(number, 10)) {
        this.betNumber = +number
        this.isButtonBlocked = false
      } else {
        event.target.classList.add('is-danger')
        this.isButtonBlocked = true
      }
    },
    getResult (hi) {
      this.isButtonBlocked = true
      const result = hi ? this.betNumber <= this.randomValue : this.betNumber > this.randomValue
      const payout = hi ? this.payoutHi : this.payoutLow
      console.log(helpers.round100(this.balance + this.betAmount*(payout-1)))
      result ? this.$emit('update:balance', this.balance + this.betAmount*(payout-1)) : this.$emit('update:balance', this.balance - this.betAmount)
      this.message = result ? this.randomValue + ' ' + 'WIN!' : this.randomValue + ' ' + 'LOSE'
      this.randomValue = this.getRandomValue()
      this.isButtonBlocked = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
.dice {
  width: 100%;
  display: flex;
  justify-content: space-around;

  .dice__pad {
    text-align: center;
    padding: 20px;

    .input__group {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin: 20px 0;

      .label {
        white-space: nowrap;
        margin-right: 10px;
        font-weight: normal;
        margin-bottom: 0;
      }
      .input {
        max-width: 150px;
        text-align: right;
      }
    }
    .buttons {
      margin: 0 -10px;
      display: flex;
      justify-content: space-between;
      .button__group {
        width: 50%;

        .button {
          margin: 10px;
          width: calc(100% - 20px);
        }
      }
    }
  }
  .dice__info {
    padding: 20px;
    width: 330px;

    .result {
      height: 84px;
    }
  }
}
</style>
