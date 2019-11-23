<template>
  <div style="width: 500px; margin: auto">
    <!-- header -->
    <div style="color: gray; width: 512px;">
      <div class="space-between" style="margin:70px 0">
        <div>
          <h1 style="font-size: 2.5em;">2048</h1>
          <p class="game-intro">Join the numbers and get to the <strong>2048 tile!</strong></p>
        </div>
        <div class="flex-center">
          <div class="blue-container" style="padding: 5px 15px; margin-right: 10px;">
            <p>score</p>
            <h2>{{ score }}</h2>
          </div>
          <div class="blue-container" style="padding: 5px 15px;">
            <p>best</p>
            <h2>{{ best }}</h2>
          </div>
        </div>
      </div>
      <div class="space-between">
        <div>
          <p class="button bold" style="margin-bottom: 40px" @click="newGame"><i class="icon">add_circle</i> New Game</p>
          <select v-model="cellRow" style="margin-left: 20px;outline: none;border: none;background: none;">
            <option value="3">3</option>
            <option value="4">4</option>
            <option value="5">5</option>
          </select>
          cells
        </div>
        <div style="font-size: 1.3em; margin-top: -40px; color: #c2c2c2">
          <p class="icon" :style="`text-align: center; width: 100%; margin-bottom: -5px; ${keyDirection === 'ArrowUp' ? 'color: black' : ''}`">arrow_drop_up</p>
          <div>
            <span class="icon" :style="`${keyDirection === 'ArrowLeft' ? 'color: black' : ''}`">arrow_left</span>
            <span class="icon" :style="`${keyDirection === 'ArrowDown' ? 'color: black' : ''}`">arrow_drop_down</span>
            <span class="icon" :style="`${keyDirection === 'ArrowRight' ? 'color: black' : ''}`">arrow_right</span>
          </div>
        </div>
      </div>
    </div>
    <!-- game container -->
    <div id="game" class="flex-center" style="width: 500px; height: 500px; flex-wrap: wrap; border: 6px solid lightgray; border-radius: 10px">
      <span style="width: 100%; height: 100%; cursor: pointer" @click="newGame">START NEW GAME</span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cellRow: 4,
      score: 0,
      best: 0,
      cellVal: [],
      cells: null,
      cellSize: null,
      gameContainer: null,
      gameStarted: false,
      keyDirection: "ArrowUp"
    };
  },
  methods: {
    newGame() {
      this.cellVal = [];
      this.cells = this.cellRow * this.cellRow;
      this.cellSize = 100 / this.cellRow;
      this.gameContainer = document.getElementById("game");

      this.game();
      this.rewriteGame();
    },

    game() {
      if (!this.gameStarted) {
        this.gameStarted = true;
        document.body.addEventListener("keyup", () => {
          ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].forEach(e => {
            if (event.key === e) (this.keyDirection = e), this.rewriteGame();
          });
        });
      }
    },

    rewriteGame() {
      this.gameContainer.innerHTML = "";
      var newNum = this.randN();
      for (let i = 0; i < this.cells; i++) {
        var cellId = `${Math.ceil((i + 1) / this.cellRow)}.${(i + 1) % this.cellRow || this.cellRow}`;
        i + 1 === newNum ? (this.cellVal[i] = 2) : (this.cellVal[i] = 0);
        this.gameContainer.appendChild(this.cell(cellId, this.cellVal[i]));
      }
      console.log(this.cellVal, newNum, this.keyDirection);
    },

    cell(cellId, cellVal) {
      var cell = document.createElement("SPAN");
      cell.id = cellId;
      cell.style = `width: ${this.cellSize}%; height: ${this.cellSize}%`;
      cellVal !== 0 ? ((cell.style.background = `hsl(${(360 / 100) * cellVal}, 100%, 30%)`), (cell.style.color = "white")) : ((cell.style.background = "none"), (cell.style.color = "none"));
      cell.innerHTML = cellVal;
      return cell;
    },

    randN() {
      return Math.floor(Math.random() * (this.cellRow * this.cellRow)) + 1;
    }

    // game() {
    //   var gameRefresh = () => {
    //     ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].forEach(e => {
    //       if (event.key === e) this.rewriteGame(e);
    //     });
    //   };

    //   if (!this.gameStarted) {
    //     this.gameStarted = true;
    //     document.body.addEventListener("keyup", gameRefresh);
    //   }
    // },
  },
  mounted() {}
};
</script>
