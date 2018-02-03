<template>
  <div id="app">

    <div class="player">
      <h1>You</h1>
      <div class="health-bar">
        <div class="content" :style="playerBar">
          <span>{{ player }}</span>
        </div>
      </div>
    </div>

    <div class="player">
      <h1>Foe</h1>
      <div class="health-bar">
        <div class="content" :style="foeBar">
          <span>{{ foe }}</span>
        </div>
      </div>
    </div>

    <div class="box" id="buttons">
      <div class="center">
        <button @click="playing = true" v-if="!playing" :style="{backgroundColor: 'lightgreen'}">
          Start
        </button>
        <div v-if="playing">

          <button @click="attack(1)" :style="{backgroundColor: 'rgba(255,0,0,0.5)'}">
            Attack
          </button>

          <button @click="attack(1.5)" :style="{backgroundColor: 'rgba(255,131,1,0.5)'}">
            Special Attack
          </button>

          <button @click="heal" :style="{backgroundColor: 'rgba(0,255,0,0.5)'}">
            Heal
          </button>

          <button @click="reset" :style="{backgroundColor: 'lightgray'}">
            Give Up
          </button>

        </div>
      </div>
    </div>

    <div class="box" id="logs" v-if="logs.length > 0">
      <div class="log-item" v-for="log in logs" :style="{backgroundColor: log.color}">
        <span>{{ log.text }}</span>
      </div>
    </div>

  </div>
</template>

<script>
var rand = function (n) {
  return Math.floor(Math.random() * n) + 1;
};

export default {
  name: 'app',
  data () {
    return {
      playing: false,
      player: 100,
      foe: 100,
      logs: []
    }
  },
  computed: {
    playerBar: function() {
      return `width: ${Math.min(400 * this.player / 100, 400)}px`;
    },
    foeBar: function () {
      return `width: ${Math.min(400 * this.foe / 100, 400)}px`;
    }
  },
  methods: {
    attack: function(factor) {
      var damage = Math.floor(rand(12) * factor);
      this.foe -= damage;
      this.logs.push({
        color: 'lightblue',
        text: `Player did ${damage} damage to foe`
      });
      this.foeAttack();
      this.checkStatus();
    },
    foeAttack: function() {
      var damage = rand(7);
      this.player -= damage;
      this.logs.push({
        color: 'rgba(255,0,0,0.5)',
        text: `Foe did ${damage} damage to player`
      });
    },
    heal: function() {
      var healing = rand(20);
      this.player += healing;
      this.logs.push({
        color: 'rgba(0,255,0,0.5)',
        text: `Player healed ${healing}`
      });
      this.foeAttack();
      this.checkStatus();
    },
    reset: function () {
      this.playing = false;
      this.player = 100;
      this.foe = 100;
      this.logs = []
    },
    checkStatus: function() {
      setTimeout(() => {
        var container = this.$el.querySelector("#logs");
        container.scrollTop = container.scrollHeight;
      }, 5)
      if (this.player <= 0) {
        alert('Foe won!');
        this.reset();
      } else if (this.foe <= 0) {
        alert('Player won!');
        this.reset();
      }
      this.player = Math.min(this.player, 100)
      this.foe = Math.min(this.foe, 100)
    }
  }
}
</script>

<style>

  * {
    margin: 0 auto;
  }

  button {
    padding: 10px;
    background-color: transparent;
    border-width: 1px;
    border-radius: 5px;
    margin: 10px;
    display: inline-block;
  }

  #app {
    width: 1048px;
    padding-top: 10px;
  }

  .center {
    margin: auto;
    width: fit-content;
  }

  .player {
    display: inline-block;
    text-align: center;
    width: 512px;
  }

  .health-bar {
    margin-top: 20px;
    width: 400px;
    height: 35px;
    border:1px solid black;
  }
  .health-bar .content {
    margin: 0;
    color: white;
    text-align: center;
    width: inherit;
    height: inherit;
    background-color: green;
    -webkit-transition: width 0.5s;
    -moz-transition: width 0.5s;
    -o-transition: width 0.5s;
    transition: width 0.5s;
  }

  .box {
    margin-top: 15px;
    border:1px solid #ababab;
    width: inherit;
    box-shadow: 0px 3px 6px #ccc;
  }

  #buttons {
    height: 60px;
  }

  #logs {
    max-height: 450px;
    overflow-y: scroll;
    width: 1024px;
  }

  .log-item {
    padding: 5px 25px 5px 25px;
  }

</style>
