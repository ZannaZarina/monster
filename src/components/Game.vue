<template>
<body>    <!-- to set a background image -->
  <div class="container">
    <section class="players d-flex my-4">

      <div class="player">
        <h4 class="player-name text-center"><strong>
          YOU
        </strong></h4>
        <div class="health-bar">
          <p class="health">{{ playerHealth }}</p>
          <div class="health-remaining" :style="{width: playerHealth + '%'}"></div>
          <div class="health-empty"></div>
        </div>
      </div>

      <div class="player">
        <h4 class="player-name text-center"><strong>
          MONSTER
        </strong></h4>
        <div class="health-bar">
          <p class="health">{{ monsterHealth }}</p>
          <div class="health-remaining" :style="{width: monsterHealth + '%'}"></div>
          <div class="health-empty"></div>
        </div>
      </div>

    </section>

    <b-card class="card mb-2 text-center d-flex">
      
      <b-button
        variant="danger"
        class="button mr-2"
        v-if="gameIsRunning"
        v-on:click="attack"
        >Attack</b-button
      >
      <b-button
        variant="warning"
        class="button mr-2"
        v-if="gameIsRunning"
        v-on:click="specialAttack"
        >Special attack</b-button
      >
      <b-button
        variant="success"
        class="button mr-2"
        v-if="gameIsRunning"
        v-on:click="heal"
        :disabled="playerHealth > 90"
        >Heal</b-button
      >
      <b-button 
        variant="primary" 
        class="button mr-2" 
        v-if="gameIsRunning"
        @click="giveUp"
        >give up</b-button
      >
      <b-button 
        variant="success"
        @click="gameIsRunning = true" 
        class="button mr-2" 
        v-else
        >Start new game</b-button
      >
    </b-card>

    <b-card class="card mb-2 text-center d-flex">
        <ul>
          <li v-for="turn in turns" 
          :key="turn.player" :class="turn.player">
          {{ turn.text }} </li>
        </ul>
    </b-card>
    
    <b-modal ref="give-up" size="sm">
      <p>You stopped the game, you lost...</p>
    </b-modal>
    
    <b-modal ref="you-won"  id="yuoWon" size="sm">
      <p>You won! Congratulations!</p>
    </b-modal>

    <b-modal ref="monster-won" id="monsterWon" variant="success" size="sm">
      <p>You lost! Try again!</p>
    </b-modal>

  </div>
  </body>
</template>

<script>
export default {
  data() {
    return {
      playerHealth: 100,
      monsterHealth: 100,
      turns: [{
        text: "",
        isPlayer: ""
      }],
      gameIsRunning: false,
    };
  },
  methods: {
    heal() {
      let heal = 10;
      this.playerHealth += heal;
      this.turns.unshift({
        text: "you heal for " + heal,
        player: 'you'
      })
      this.monsterTurn();
      this.healthCheck()
    },
    attack() {
      let kick = Math.floor(Math.random() * 10) + 1;
      this.monsterHealth -= kick;
      this.turns.unshift({
        text: "you hit monster for " + kick,
        player: 'you'
      })
      this.monsterTurn();
      this.healthCheck()

    },
    specialAttack: function() {
      let kick = Math.floor(Math.random() * 11) + 10;
      this.monsterHealth -= kick;
      this.turns.unshift({
        text: "you hit monster for " + kick,
        player: 'you'
      })
      this.monsterTurn();
      this.healthCheck()
    },
    monsterTurn() {
      let kick = Math.floor(Math.random() * 11) + 5;
      this.playerHealth -= kick;
      this.turns.unshift({
        text: "monster hits you for " + kick,
        player: 'monster'
      })
    },
    giveUp() {
          this.$refs['give-up'].show()
          this.playerHealth = 100,
          this.monsterHealth = 100,
          this.turns = [],
          this.gameIsRunning = false
    },
    healthCheck() {
      if (this.monsterHealth <= 0) {
          this.$refs['you-won'].show()
          this.playerHealth = 100,
          this.monsterHealth = 100,
          this.turns = [],
          this.gameIsRunning = false

      } else if (this.playerHealth <= 0) {
          this.$refs['monster-won'].show()
          this.playerHealth = 100,
          this.monsterHealth = 100,
          this.turns = [],
          this.gameIsRunning = false
      }
    },

  }
};
</script>

<style scoped>
* {
  box-sizing: border-box;
}

body {
  background-image: url('../assets/monster.jpg');
  padding-top: 50px;
}

.container {
  max-width: 1000px;
  min-height: 900px;;
  height: max-content;
  background-repeat: repeat;  
}

#monsterWon {
  font-size: 50px;
}

.player {
  width: 50%;
}

.health-bar {
  position: relative;
  width: 90%;
  height: 40 px;
  background-color: rgb(170, 169, 169);
  margin: auto auto;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.404), 0px 1px 3px rgba(0, 0, 0, 0.301), inset 0px 1px 0px rgba(255, 255, 255, 0);

}

.health {
  position: relative;
  z-index: 100;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: white;
  font-size: 20px;
}

.health-empty {
  height: 40px;
  background-color: transparent;
}

.health-remaining {
  background-color: rgb(0, 155, 0);
  height: 100%;
  width: 100%;
  position: absolute;
}

.card{
  background:rgba(0, 0, 0, 0.137);
}

.button {
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.404), 0px 1px 3px rgba(0, 0, 0, 0.301), inset 0px 1px 0px rgba(255, 255, 255, 0);
}

ul {
    list-style: none;
    padding: 0;
}

li.you {
  background-color: rgba(143, 143, 143, 0.253);
  color:rgb(61, 55, 55);
  font-weight: bold;
}

li.monster {
  background-color: rgba(224, 173, 173, 0.26);
  color:rgb(206, 26, 26);
  font-weight: bold;
}

  /* 
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%); 
  */

</style>
