<template>
  <div style="width: 500px; margin: auto">
    <!-- header -->
    <div style="color: gray; width: 512px;">
      <div class="space-between" style="margin:70px 0">
        <div>
          <div class="space-between">
            <h1 style="font-size: 2.5em;">2048</h1>
            <div><input type="radio" name="level" value="easy" v-model="level" /><span style="padding-right: 7px"> Easy</span> <input type="radio" name="level" value="hard" v-model="level" />Hard</div>
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
          <p class="button bold" style="margin-bottom: 40px" @click="newGame"><i class="icon">sports_esports</i> New Game</p>
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
      <span style="width: 100%; height: 100%; cursor: pointer" @click="newGame"><i class="icon" style="padding-right: 20px; font-size: 3em; color: dodgerblue">sports_esports</i>START NEW GAME</span>
    </div>
    <!-- footer -->
    <transition>
      <div style="margin: 20px ;font-size: 0.8em; color: gray">
        <p>* Easy - the sum of all the adding operations during the game.</p>
        <p>* Hard - the sum of all the values at the field.</p>
      </div>
    </transition>
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
    /** New game */
    newGame() {
      this.scoreEasy = 0;
      this.scoreHard = 0;
      this.cellVal = [0];
      this.cells = this.cellRow * this.cellRow;
      this.cellSize = 100 / this.cellRow;
      this.gameContainer = document.getElementById("game");

      if (!this.gameStarted) {
        this.gameStarted = true;
        document.body.addEventListener("keydown", this.game);
      }
      this.rewriteGame();
    },

    /** Inint method */
    game() {
      ["ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight"].forEach(e => {
        if (event.key === e) {
          event.preventDefault();
          this.keyDirection = e;
          this.rewriteGame();
        }
      });
    },

    /** Game engine
     *
     */
    rewriteGame() {
      // clear the game field
      this.gameContainer.innerHTML = "";

      // creates a temporary array and pre populate nested arrays at the count of cellRow value
      var tempArr = [];
      for (let i = 0; i < this.cellRow; i++) {
        tempArr.push([]);
      }

      // populates the temp array with the values from this.cellVal[] and orienting it vertically || horizontally according to the key pressed
      for (let i = 0; i < this.cells; i++) {
        var row = Math.ceil((i + 1) / this.cellRow) - 1;
        var col = i % this.cellRow;
        this.keyDirection === "ArrowRight" || this.keyDirection === "ArrowLeft" ? (tempArr[row][col] = this.cellVal[i] || 0) : (tempArr[col][row] = this.cellVal[i] || 0);
      }

      // calculate nested arrays in the temp array separately [ltr] or [rtl] (push all the vals to the end and merge equal vals)
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

      // local helper for this function (shifts the zeros to the end)
      function orderZerosInArray(arr, rtl = false) {
        if (Number(arr.join("")) === 0) return arr;
        if (rtl) {
          for (let i = arr.length; i >= 0; i--) {
            if (arr[i] === 0) {
              arr.splice(i, 1);
              arr.splice(arr.length, 0, 0);
            }
          }
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

      // New number to play with
      var newNum = this.nextNumber();

      // GAME OVER
      if (!newNum) {
        this.gameStarted = false;
        document.body.removeEventListener("keydown", this.game);
        return (document.getElementById("game").innerHTML = `<span style="width: 100%; height: 100%; text-align: center;">GAME OVER<br><span style="font-size: 0.5em; width: 50%; box-shadow: none; font-weight: normal; padding-right: 20px;">Your score is ${this.level === "easy" ? this.scoreEasy : this.scoreHard} points</span></span>`);
      }

      // populates the game field with the values of this.cellVal[]
      for (let i = 0; i < this.cells; i++) {
        var newCell = null;
        if (i + 1 === newNum) {
          this.cellVal[i] = 2;
          newCell = this.cell(2);
          newCell.className = "enter";
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

      // calculates the score
      this.scoreHard = this.cellVal.reduce((a, b) => a + b, 0);
      if (this.bestScore < this.scoreEasy && this.level == "easy") {
        this.bestScore = this.scoreEasy;
      } else if (this.bestScore < this.scoreHard && this.level == "hard") {
        this.bestScore = this.scoreHard;
      }
    },

    /** Helper method
     *  generates a new cell
     */
    cell(cellVal) {
      var cell = document.createElement("SPAN");
      cell.style = `width: ${this.cellSize}%; height: ${this.cellSize}%`;

      if (cellVal !== 0) {
        cell.style.background = `hsl(${cellVal * 100}, 100%, 30%)`;
        cell.style.color = "white";

        switch (this.keyDirection) {
          case "ArrowUp": {
            cell.className = "from-down";
            break;
          }
          case "ArrowDown": {
            cell.className = "from-up";
            break;
          }
          case "ArrowLeft": {
            cell.className = "from-right";
            break;
          }
          case "ArrowRight": {
            cell.className = "from-left";
            break;
          }
        }
      }

      cell.innerHTML = cellVal;
      return cell;
    },

    /** Helper method
     *  generates a new Number for play with and check if the number is not occupied
     *  if no available numbers, returns false ( trigger for game over )
     */
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

<style>
/** Animations
 *  
 */
.enter {
  animation: bounce 0.7s;
}

@keyframes bounce {
  0% {
    transform: scale(0.1);
    opacity: 0;
  }

  70% {
    transform: scale(1.1);
    opacity: 1;
  }

  100% {
    transform: scale(1);
  }
}

.from-left {
  animation: from-left 0.2s;
}

@keyframes from-left {
  0% {
    opacity: 0;
    transform: translateX(-100px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}

.from-right {
  animation: from-right 0.2s;
}

@keyframes from-right {
  0% {
    opacity: 0;
    transform: translateX(100px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}

.from-up {
  animation: from-up 0.2s;
}

@keyframes from-up {
  0% {
    opacity: 0;
    transform: translateY(-100px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

.from-down {
  animation: from-down 0.2s;
}

@keyframes from-down {
  0% {
    opacity: 0;
    transform: translateY(100px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

/** General CSS
 *  
 */
@import url("https://fonts.googleapis.com/css?family=Open+Sans:300,500,700&subset=cyrillic");
@import url("https://fonts.googleapis.com/icon?family=Material+Icons");
body {
  font-family: "Open Sans", Verdana, Geneva, sans-serif;
  font-size: 0.9em;

  -moz-user-select: none;
  -khtml-user-select: none;
  -webkit-user-select: none;

  -ms-user-select: none;
  user-select: none;
}

.icon {
  font-family: "Material Icons";
  font-style: normal;
  font-weight: normal;
  font-size: 1.3em;
  vertical-align: bottom;
  display: inline-block;

  -webkit-font-smoothing: antialiased;
  text-rendering: optimizeLegibility;
  -moz-osx-font-smoothing: grayscale;
  font-feature-settings: "liga";
}

.flex-center {
  display: flex;
  align-items: center;
}

.space-between {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.bold {
  font-weight: 700;
}

p,
h1,
h2,
h3,
h4,
h5,
h6 {
  padding: 0;
  margin: 0;
}

.button {
  display: inline-block;
  background: white;
  color: dodgerblue;
  padding: 10px 15px;
  border-radius: 5px;
  border: 1px solid lightgray;
  border-bottom: 2px solid lightgray;
}

.button:hover {
  box-shadow: 0px 2px 7px rgba(0, 0, 0, 0.15);
  cursor: pointer;
}

.button:active {
  box-shadow: inset 0 1px 0px 1px rgba(0, 0, 0, 0.15), inset 0 1px 7px 4px rgba(0, 0, 0, 0.15);
}

.blue-container {
  border: 1px solid dodgerblue;
  border-radius: 5px;
  text-align: center;
}

#game span {
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: inset 0 0 0px 7px lightgray;
  color: gray;
  width: 25%;
  height: 25%;
  font-size: 2em;
  font-weight: bold;
}
</style>
