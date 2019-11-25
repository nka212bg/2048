<template>
  <div style="width: 500px; margin: auto">
    <!-- header -->
    <div style="color: gray; width: 512px;">
      <div class="space-between" style="margin:70px 0">
        <div>
          <div class="space-between">
            <h1 style="font-size: 2.5em;">2048</h1>
            <div><input type="radio" name="level" value="easy" v-model="level" />Easy <input type="radio" name="level" value="hard" v-model="level" />Hard</div>
          </div>
          <p class="game-intro">Join the numbers and get to the <strong>2048 tile!</strong></p>
        </div>
        <div class="flex-center">
          <div class="blue-container" style="padding: 5px 15px; margin-right: 10px;">
            <p>score</p>
            <h2>{{ level === "easy" ? scoreEasy : scoreHard }}</h2>
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
            <option value="6">6</option>
          </select>
          cells
        </div>
        <div style="font-size: 1.3em; margin-top: -53px; color: #c2c2c2">
          <p class="icon" :style="`text-align: center; width: 100%; margin-bottom: -13px; ${keyDirection === 'ArrowUp' ? 'color: black' : ''}`">arrow_drop_up</p>
          <div>
            <span class="icon" :style="`${keyDirection === 'ArrowLeft' ? 'color: black' : ''}`">arrow_left</span>
            <span class="icon" :style="`${keyDirection === 'ArrowRight' ? 'color: black' : ''}`">arrow_right</span>
          </div>
          <p class="icon" :style="`text-align: center; width: 100%; margin-top: -13px; display: block; ${keyDirection === 'ArrowDown' ? 'color: black' : ''}`">arrow_drop_down</p>
        </div>
      </div>
    </div>
    <!-- game container -->
    <div id="game" class="flex-center" style="width: 500px; height: 500px; flex-wrap: wrap; border: 6px solid lightgray; border-radius: 10px">
      <span style="width: 100%; height: 100%; cursor: pointer" @click="newGame">START NEW GAME</span>
    </div>
    <!-- footer -->
    <div style="margin-top: 20px ;font-size: 0.8em; color: gray">
      <p>* Easy - all the adding operations during the game.</p>
      <p>* Hard - the sum of all the values at the field.</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cellRow: 4,
      scoreEasy: 0,
      scoreHard: 0,
      bestScore: 0,
      level: "easy",
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
      this.scoreEasy = 0;
      this.scoreHard = 0;
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

      // this.cellVal[] to array with nested arrays
      for (let i = 0; i < this.cells; i++) {
        var row = Math.ceil((i + 1) / this.cellRow) - 1;
        var col = i % this.cellRow;
        this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowLeft" ? (tempArr[row][col] = this.cellVal[i] || 0) : (tempArr[col][row] = this.cellVal[i] || 0);
      }

      // calculate nested arrays separately ltr (push all the vals to right and merge equal vals)
      tempArr.forEach(e => {
        for (let w = 0; w < e.length; w++) {
          if (this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowDown") {
            // move all zeros to the beginning [ltr]
            e = orderZerosInArray(e);

            // add the equal vals that sits to eachother [ltr]
            if (e[w] === e[w + 1] && e[w] !== 0) {
              var cellResult = e[w + 1] * 2;
              this.scoreEasy += cellResult;
              e[w + 1] = cellResult;
              e[w] = 0;
            } else if (e[w] === e[w - 1] && e[w] !== 0) {
              var cellResult = e[w] * 2;
              this.scoreEasy += cellResult;
              e[w] = cellResult;
              e[w - 1] = 0;
            }
          } else {
            // move all zeros to the end [rtl]
            e = orderZerosInArray(e, true);
            // add the equal vals that sits to eachother [rtl]
            if (e[w] === e[w - 1] && e[w] !== 0) {
              var cellResult = e[w - 1] * 2;
              this.scoreEasy += cellResult;
              e[w - 1] = cellResult;
              e[w] = 0;
            } else if (e[w] === e[w + 1] && e[w] !== 0) {
              var cellResult = e[w] * 2;
              this.scoreEasy += cellResult;
              e[w] = cellResult;
              e[w + 1] = 0;
            }
          }
        }
      });

      function orderZerosInArray(arr, rtl = false) {
        if (Number(arr.join("")) === 0) return arr;

        if (rtl) {
          for (let i = arr.length; i >= 0; i--) {
            if (arr[i] === 0) {
              arr.splice(i, 1);
              arr.splice(arr.length, 0, 0);
            }
          }
          /** alternative way */
          // arr.reverse();
          // for (let i = 0; i < arr.length; i++) {
          //   if (arr[i] === 0) {
          //     arr.splice(i, 1);
          //     arr.splice(0, 0, 0);
          //   }
          // }
          // arr.reverse();
        } else {
          for (let i = 0; i < arr.length; i++) {
            if (arr[i] === 0) {
              arr.splice(i, 1);
              arr.splice(0, 0, 0);
            }
          }
        }

        return arr;
      }

      // reverse the nested array to an one level array this.cellVal[]
      this.cellVal = [];
      if (this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowLeft") {
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

      var newNum = this.nextNumber();
      if (!newNum) {
        // GAME OVER
        return (document.getElementById("game").innerHTML = `<span style="width: 100%; height: 100%; text-align: center;">GAME OVER<br><span style="font-size: 0.5em; width: 50%; box-shadow: none; font-weight: normal; padding-right: 20px;">Your score is ${this.level === "easy" ? this.scoreEasy : this.scoreHard} points</span></span>`);
      }
      for (let i = 0; i < this.cells; i++) {
        var newCell = null;
        if (i + 1 === newNum) {
          this.cellVal[i] = 2;
          newCell = this.cell(2);
          newCell.style.boxShadow = "lightgrey 0px 0px 0px 8px inset, green 0px 0px 0px 10px inset";
          newCell.style.background = "white";
          newCell.style.color = "green";
        } else if (!this.cellVal[i]) {
          this.cellVal[i] = 0;
          newCell = this.cell(0);
        } else {
          newCell = this.cell(this.cellVal[i]);
        }
        this.gameContainer.appendChild(newCell);
      }

      this.scoreHard = this.cellVal.reduce((a, b) => a + b, 0);
      if (this.bestScore < this.scoreEasy && this.level == "easy") {
        this.bestScore = this.scoreEasy;
      } else if (this.bestScore < this.scoreHard && this.level == "hard") {
        this.bestScore = this.scoreHard;
      }
    },

    cell(cellVal) {
      var cell = document.createElement("SPAN");
      cell.style = `width: ${this.cellSize}%; height: ${this.cellSize}%`;
      cellVal !== 0 ? ((cell.style.background = `hsl(${(360 / 100) * cellVal}, 100%, 30%)`), (cell.style.color = "white")) : ((cell.style.background = "none"), (cell.style.color = "none"));
      cell.innerHTML = cellVal;
      return cell;
    },

    nextNumber() {
      if (this.cellVal.includes(0)) {
        var nextNumber = Math.floor(Math.random() * (this.cellRow * this.cellRow));
        if (this.cellVal[nextNumber] === 0) {
          return nextNumber + 1;
        } else {
          return this.nextNumber();
        }
      } else {
        return false;
      }
    }
  }
};
</script>
