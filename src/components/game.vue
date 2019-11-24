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
            <h2>{{ bestScore }}</h2>
          </div>
        </div>
      </div>
      <div class="space-between">
        <div>
          <p class="button bold" style="margin-bottom: 40px" @click="newGame"><i class="icon">games</i> New Game</p>
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
      bestScore: 0,
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
      this.cellVal = [0];
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
      var tempArr = [];
      for (let i = 0; i < this.cellRow; i++) {
        tempArr.push([]);
      }

      // this.cellVal[] to array with nested arrays horizontaly
      for (let i = 0; i < this.cells; i++) {
        var row = Math.ceil((i + 1) / this.cellRow) - 1;
        var col = i % this.cellRow;
        this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowLeft" ? (tempArr[row][col] = this.cellVal[i]) : (tempArr[col][row] = this.cellVal[i]);

        // if (this.keyDirection === "ArrowDown" || this.keyDirection === "ArrowUp") {
        //   tempArr[col][row] = this.cellVal[i];
        // }
      }

      // calculate nested arrays separately ltr (push all the vals to right and merge equal vals)
      tempArr.forEach(e => {
        for (let w = 0; w < e.length; w++) {
          if (this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowDown") {
            // move all zeros to the beginning
            if (e[w] === 0) {
              e.splice(w, 1);
              e.splice(0, 0, 0);
            }
          } else {
            // move all zeros to the end
            if (e[w] === 0) {
              e.splice(w, 1);
              e.splice(e.length - 1, 0, 0);
            }
          }

          // add the equal vals that sits to eachother
        }
      });

      // reverse the nested array to an one level array this.cellVal[]
      console.log(this.cellVal);
      this.cellVal = [];
      if (this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowLeft") {
        // console.log(tempArr);
        for (let i = 0; i < tempArr.length; i++) {
          for (let w = 0; w < tempArr[i].length; w++) {
            this.cellVal.push(tempArr[i][w]);
          }
        }
      } else {
        for (let i = 0; i < tempArr.length; i++) {
          for (let w = 0; w < tempArr[i].length; w++) {
            this.cellVal.push(tempArr[w][i]);
          }
        }
      }

      // console.log(this.cellVal);

      var newNum = this.nextNumber();
      for (let i = 0; i < this.cells; i++) {
        var cellId = `${Math.ceil((i + 1) / this.cellRow)}.${(i + 1) % this.cellRow || this.cellRow}`;
        i + 1 === newNum ? (this.cellVal[i] = 2) : (this.cellVal[i] = 0);
        this.gameContainer.appendChild(this.cell(cellId, this.cellVal[i]));
      }

      this.score = this.cellVal.reduce((a, b) => a + b, 0);
      if (this.bestScore < this.score) this.bestScore = this.score;

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

    nextNumber() {
      if (this.cellVal.includes(0)) {
        var nextNumber = Math.floor(Math.random() * (this.cellRow * this.cellRow));
        if (this.cellVal[nextNumber] === 0) {
          // console.log(nextNumber);
          return nextNumber + 1;
        } else {
          return this.nextNumber();
        }
      } else {
        this.gameOver();
      }
    },

    gameOver() {
      console.log("Game over");
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
