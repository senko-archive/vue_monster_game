<template>
  <div id="app">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <div class="healthbar" v-bind:style="{width : playerHealth+'%'}">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
          > {{ playerHealth }} </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <div class="healthbar" v-bind:style="{width : monsterHealth+'%'}">
          <div
            class="healthbar text-center"
            style="background-color: green; margin: 0; color: white;"
      > {{ monsterHealth}} </div>
        </div>
      </div>
    </section>
    <section v-if="!isGameActive" class="row controls">
      <div class="small-12 columns">
        <button id="start-game" v-on:click="start_game()">YENİ OYUN</button>
      </div>
    </section>
    <section v-if="isGameActive" class="row controls">
      <div class="small-12 columns">
        <button id="attack" v-on:click="attack()">SALDIRI</button>
        <button id="special-attack" v-on:click="specialAttack()">ÖZEL SALDIRI</button>
        <button id="heal" v-on:click="healUp()">İLK YARDIM</button>
        <button id="give-up" v-on:click="giveUp()">PES ET!</button>
      </div>
    </section>

    <section v-if="isGameActive && gameLog.length > 0" class="row log">
      <div class="small-12 columns">
        <ul v-for="(log, index) in gameLog" v-bind:key=index>
          <li v-bind:class="{'player-turn' : log.turn == 'player', 
                             'monster-turn' : log.turn == 'monster'}"> {{ log.text }} </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isGameActive: false,
      monsterHealth: 100,
      playerHealth: 100,
      gameLog: []
    };
  },
  methods: {
    start_game() {
      this.isGameActive = true;
    },
    attack() {
      let dmg = Math.ceil(Math.random() * 10);
      this.monsterHealth -= dmg;
      this.addToLog({turn: "player", text: "Player Attack " + dmg })
      this.monsterAttack();
      this.logHealths();
    },
    monsterAttack() {
      let dmg = Math.ceil(Math.random() * 10);
      this.playerHealth -= dmg;
      this.addToLog({turn: "monster", text: "Monster Attack " + dmg })
    },
    specialAttack() {
      let dmg = Math.ceil(Math.random() * 25);
      this.monsterHealth -= dmg;
      this.addToLog({turn: "player", text: "Player Special Attack " + dmg })
      this.monsterAttack();
      this.logHealths();
    },
    healUp() {
      let heal = Math.ceil(Math.random() * 10);
      this.playerHealth += heal;
      this.addToLog({turn: "player", text: "Player Healup " + heal })
      this.monsterAttack();
      this.logHealths();
    },
    giveUp() {
      this.playerHealth = 0;
      this.logHealths();
    },

    logHealths() {
      console.log("Player Health: " + this.playerHealth);
      console.log("Monster Health: " + this.monsterHealth);
    },
    restartGame() {
      this.isGameActive = false;
      this.playerHealth = 100;
      this.monsterHealth = 100;
      this.gameLog = [];
    },
    addToLog(logData) {
      this.gameLog.push(logData)
    }
  },
  watch: {
    playerHealth(value) {
      if(value <= 0) {
        this.playerHealth = 0;
        let decision = confirm("You lose the game, do you want to play again?")
        if(decision) {
          this.restartGame();
        }
      }
      else if(value > 100) {
        this.playerHealth = 100;
      }
    },
    monsterHealth(value) {
      if(value <= 0) {
        this.monsterHealth = 0;
        let decision = confirm("You win the game, do you want to play again?");
        if(decision) {
          this.restartGame()
        }
      }
    }
  }
};
</script>

<style>
@import "./assets/foundation.min.css";
@import "./assets/app.css";
</style>
