<template>
  <div id="app">
    <div class="panel scores">
      <div class="score">
        <h1>Jogador</h1>
        <div class="life-bar">
          <div class="life" :class="{ danger: playerLife < 20 }" :style="{ width: playerLife + '%' }"></div>
          <div class="life-players">{{ playerLife }}%</div>
        </div>
      </div>

      <div class="score">
        <h1>Monstro</h1>
        <div class="life-bar">
          <div class="life" :class="{ danger: monsterLife < 20 }" :style="{ width: monsterLife + '%' }"></div>
          <div class="life-players">{{ monsterLife }}%</div>
        </div>
      </div>
    </div>

    <div v-if="result" class="panel result">
      <div class="result">
        <template>
          <div v-if="playerLife > 0 && monsterLife == 0">
            <p>Você ganhou! ^^)</p>
          </div>
          <div v-else>
            <p>Você perdeu! :/</p>
          </div>
        </template>
      </div>
    </div>

    <div class="panel buttons">
      <template v-if="running">
        <button class="btn atack" @click="atack(false)">Ataque</button>
        <button class="btn especial-atack" @click="atack(true)">
          Ataque Especial
        </button>
        <button class="btn health" @click="health(true)">Curar</button>
        <button class="btn give-up" @click="reset">Desistir</button>
      </template>
      <button v-else class="btn start" @click="start">Iniciar jogo</button>
    </div>

    <div v-if="logs.length" class="panel logs">
      <ul>
        <li v-for="(log, index) in logs" class="log" :key="index">
          {{ log.text }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      running: false,
      playerLife: 100,
      monsterLife: 100,
      logs: [],
    };
  },
  computed: {
    result() {
      return this.playerLife == 0 || this.monsterLife == 0;
    },
  },
  methods: {
    getRandom(min, max) {
      const value = Math.random() * (max - min) + min;
      return Math.round(value);
    },
    start() {
      this.running = true;
      this.playerLife = 100;
      this.monsterLife = 100;
      this.logs = [];
    },
    reset() {
      this.running = false;
      this.playerLife = 100;
      this.monsterLife = 100;
      this.logs = [];
    },
    atack(especial) {
      this.strength('monsterLife', 6, 10, especial, 'Jogador', 'Monstro', 'player')
      if (this.monsterLife > 0) {
        this.strength('playerLife', 7, 12, false, 'Monstro', 'Jogador', 'monster')
      }
    },
    strength(character, min, max, especial, source, target, classe) {
      const plus = especial ? 5 : 0;
      const powerAtack = this.getRandom(min + plus, max + plus);
      this[character] = Math.max(this[character] - powerAtack, 0);
      this.registerLog(`${source} atingiu ${target} com ${this.strength}.`,classe);
    },
    health(heal_) {
      this.heal("playerLife", 1, 3, heal_);
    },
    heal(character, min, max, heal_ = true) {
      if (this[character] === 100) return;
      const addHealth = heal_ ? 5 : 0;
      const heal = this.getRandom(min + addHealth, max + addHealth);
      this[character] = Math.min(this[character] + heal, 100);
      this.strength("playerLife", 3, 10, false);
      this.registerLog(`Jogador aumentou a vida em ${heal}.`, "player");
    },
    registerLog(text, classe) {
      this.logs.unshift({ text, classe });
    },
  },
  watch: {
    result(value) {
      if (value == true) {
        this.running = false;
      }
    },
  },
};
</script>

<style>
/*General*/

html {
  font-family: "Montserrat", sans-serif;
}

#app {
  display: flex;
  flex-direction: column;
}

.panel {
  display: flex;
  margin: 12px;
  padding: 20px;
  box-shadow: 0 2px 10px black;
}

/*Scores*/

.scores {
  display: flex;
}

.score {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.life-bar {
  width: 80%;
  height: 25px;
  border: 1px solid black;
  text-align: center;
}

.life-bar .life {
  display: flex;
  justify-content: center;
  height: 100%;
  background-color: green;
}

.life-bar .life.danger {
  background-color: red;
}

.life-players {
  padding: 3px;
}

/*Result*/

.result {
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 600;
}

/*Buttons*/

.buttons {
  display: flex;
  justify-content: center;
}

.btn {
  border-radius: 10px;
  color: black;
  background: gold;
  margin: 8px;
  padding: 10px;
  font-size: 1rem;
}

/*Logs*/

.logs {
  display: flex;
  flex-direction: column;
  list-style: none;
  padding: 0px;
  margin: 0px;
}

.logs ul li {
  display: flex;
  justify-content: center;
  margin: 4px 0px;
  padding: 3px 0px;
  font-weight: 600;
  font-size: 1rem;
  text-transform: uppercase;
  border-radius: 5px;
}

.log .player {
  background-color: #4243afaa;
  color: white;
}

.log .monster {
  background-color: #e51c23aa;
  color: white;
}
</style>