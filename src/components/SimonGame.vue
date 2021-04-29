<template>
  <div class='container'>
    <div class="row">
      <h2 class="simon-header">Игра Саймон</h2>
    </div>
    <div class="row">  
      <p class="game-info" v-if="simon.level != ''">{{ simon.level }}</p>
    </div>
    <div class="row">
      <Tile color='yellow' :isActivated="simon.yellowActivated" :isClickable="simon.isClickable" :clickFn="handleClick"/>
      <Tile color='blue' :isActivated="simon.blueActivated" :isClickable="simon.isClickable" :clickFn="handleClick"/>
    </div>
    <div class="row">  
      <Tile color='red' :isActivated="simon.redActivated" :isClickable="simon.isClickable" :clickFn="handleClick"/>
      <Tile color='green' :isActivated="simon.greenActivated" :isClickable="simon.isClickable" :clickFn="handleClick"/>
    </div>
    <div class="row">
      <button class="btn-start" @click="startGame">Старт</button>
    </div>
    <div class="row">
      <div class="difficulty">
        <label class="difficulty-name" for="difficulty">Сложность: </label>
        <div class="difficulty-item-wrapper">
          <label class="radio-label"><input v-model="simon.difficulty" class="difficulty-input" type="radio" name="difficulty" value="1500">
            <div class="difficulty-radio"></div>Лёгкий
          </label>
        </div>
        <div class="difficulty-item-wrapper">
          <label class="radio-label"><input v-model="simon.difficulty" class="difficulty-input" type="radio" name="difficulty" value="1000">
            <div class="difficulty-radio"></div>Средний
          </label>
        </div>
        <div class="difficulty-item-wrapper">
          <label class="radio-label"><input v-model="simon.difficulty" class="difficulty-input" type="radio" name="difficulty" value="400">
            <div class="difficulty-radio"></div>Сложный
          </label>
        </div>
      </div>
    </div>
    <div style="display:none">
      <audio src="../../public/sounds/simonSound1.mp3" ref="yellow-sound"></audio>
      <audio src="../../public/sounds/simonSound2.mp3" ref="blue-sound"></audio>
      <audio src="../../public/sounds/simonSound3.mp3" ref="red-sound"></audio>
      <audio src="../../public/sounds/simonSound4.mp3" ref="green-sound"></audio>
    </div>
    {{ simon }}
  </div>
</template>

<script>
import Tile from './Tile.vue'

export default {
  name: 'SimonGame',
  components: {
    Tile
  },
  data() {
    return {
      simon: {
        difficulty: '1500',
        computerSeq: [],
        userSeq: [],
        gameMessage: '',
        level: 0,
        redActivated: '',
        blueActivated: '',
        greenActivated: '',
        yellowActivated: '',
        isClickable: false,
      }
    }
  },
  created: function() {
    this.simon.isClickable = false
  },
  methods: {
    startGame: function() {
      this.simon.gameMessage = "Ждите хода компьютера";
      this.nextLevel();
    },
    nextLevel: function() {
      this.simon.level += 1;

      this.simon.isClickable = false
      this.simon.gameMessage = "Ждите хода компьютера";


      const nextSeq = [...this.simon.computerSeq];
      nextSeq.push(this.nextStep());
      this.playRound(nextSeq);

      this.simon.computerSeq = [...nextSeq];
      setTimeout(() => {
        this.playerTurn(this.simon.level);
      }, this.simon.level * Number(this.simon.difficulty) + 1000);
    },
    nextStep: function() {
      const tiles = ['yellow','blue','red','green'];

      const generate = tiles[Math.floor(Math.random() * tiles.length)]
      return generate;
    },
    activateTile: function(color) {
      const sound = this.$refs[color + '-sound'];
      this.simon[color + 'Activated'] = true 
      sound.play();

      setTimeout(() => {
        this.simon[color + 'Activated'] = false
      }, 300);
    },
    playRound: function(nextSeq) {
      nextSeq.forEach( (color,index) => {
        setTimeout(() => {
          this.activateTile(color);
        }, (index + 1) * Number(this.simon.difficulty) );
      });
    },
    playerTurn: function(level) {
      this.simon.isClickable = true
      this.simon.gameMessage = `Ваш ход: Повтори ${ level } раз. `
    },
    handleClick: function(tile) {
      let userSeqArray = this.simon.userSeq;
      const index = userSeqArray.push(tile) - 1;
      this.simon.userSeq = userSeqArray;
      const sound = this.$refs[tile + '-sound'];
      sound.play();

      const remainingTaps = this.simon.computerSeq.length - this.simon.userSeq.length;

       if (this.simon.userSeq[index] !== this.simon.computerSeq[index]) {
        this.resetGame('Oops! Game over, you pressed the wrong tile');
        return;
       }

      if (this.simon.userSeq.length === this.simon.computerSeq.length) {
         if (this.simon.userSeq.length === 20) {
            this.resetGame('Congrats! You completed all the levels');
            return
          }

        this.simon.userSeq = [];
        this.simon.gameMessage = "Молодец! Продолжай";
        setTimeout(() => {
          this.nextLevel();
        }, 1000);
        return;
      }

      this.simon.gameMessage = `Ваш ход: ${remainingTaps} осталось клеток`;
    },
    resetGame: function(text) {
      alert(text);
      this.simon.userSeq = [];
      this.simon.computerSeq = [];
      this.simon.level = 0;
      this.simon.gameMessage = '';
      this.simon.isClickable = false;
    },
    playSound: function(color) {
      //This is debug function
      let sound = this.$refs[color + '-sound'];
      let promise = sound.play();
      if (promise) {
          //Older browsers may not return a promise, according to the MDN website
          promise.catch(function(error) { console.error(error); });
      }
      console.log(this.$refs[color + '-sound']);
      sound.play();
    }

  }
}
</script>


<style lang="sass" scoped>
@import './SimonGame'

</style>
